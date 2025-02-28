# atividade03
--> Classe ContaBancaria
public abstract class ContaBancaria {
    protected double saldo;
    protected String numeroConta;
    protected String titular;

    public ContaBancaria(String numeroConta, String titular, double saldo) {
        this.numeroConta = numeroConta;
        this.titular = titular;
        this.saldo = saldo;
    }

    public void depositar(double valor) {
        if (valor > 0) {
            saldo += valor;
            System.out.println("Depósito de R$" + valor + " realizado. Novo saldo: R$" + saldo);
        } else {
            System.out.println("Valor inválido para depósito.");
        }
    }

    public abstract void sacar(double valor);

    public void exibirInformacoes() {
        System.out.println("\nTitular: " + titular);
        System.out.println("Número da Conta: " + numeroConta);
        System.out.println("Saldo: R$" + saldo);
    }
}

--> Classe ContaCorrente
public class ContaCorrente extends ContaBancaria {
    private double limiteChequeEspecial;

    public ContaCorrente(String numeroConta, String titular, double saldo, double limiteChequeEspecial) {
        super(numeroConta, titular, saldo);
        this.limiteChequeEspecial = limiteChequeEspecial;
    }

    @Override
    public void sacar(double valor) {
        if (valor > 0 && saldo + limiteChequeEspecial >= valor) {
            saldo -= valor;
            System.out.println("Saque de R$" + valor + " realizado. Novo saldo: R$" + saldo);
        } else {
            System.out.println("Saldo insuficiente para saque.");
        }
    }
}

--> Classe ContaCorrente
public class ContaPoupanca extends ContaBancaria {

    public ContaPoupanca(String numeroConta, String titular, double saldo) {
        super(numeroConta, titular, saldo);
    }

    @Override
    public void sacar(double valor) {
        if (valor > 0 && saldo >= valor) {
            saldo -= valor;
            System.out.println("Saque de R$" + valor + " realizado. Novo saldo: R$" + saldo);
        } else {
            System.out.println("Saque não permitido. Saldo insuficiente.");
        }
    }
}

--> Classe ContaInvestimento
public class ContaInvestimento extends ContaBancaria {

    public ContaInvestimento(String numeroConta, String titular, double saldo) {
        super(numeroConta, titular, saldo);
    }

    @Override
    public void sacar(double valor) {
        double taxa = valor * 0.02;
        double valorFinal = valor + taxa;
        if (valor > 0 && saldo >= valorFinal) {
            saldo -= valorFinal;
            System.out.println("Saque de R$" + valor + " realizado com taxa de R$" + taxa + ". Novo saldo: R$" + saldo);
        } else {
            System.out.println("Saque não permitido. Saldo insuficiente.");
        }
    }
}

--> Conta ContaSalario
public class ContaSalario extends ContaCorrente {
    private int saquesGratuitos = 1;

    public ContaSalario(String numeroConta, String titular, double saldo, double limiteChequeEspecial) {
        super(numeroConta, titular, saldo, limiteChequeEspecial);
    }

    @Override
    public void sacar(double valor) {
        if (valor > 0 && saldo + super.limiteChequeEspecial >= valor) {
            double taxa = (saquesGratuitos > 0) ? 0 : 5.00;
            double valorFinal = valor + taxa;

            if (saldo + super.limiteChequeEspecial >= valorFinal) {
                saldo -= valorFinal;
                System.out.println("Saque de R$" + valor + " realizado. Taxa: R$" + taxa + ". Novo saldo: R$" + saldo);
                saquesGratuitos--;
            } else {
                System.out.println("Saldo insuficiente para saque.");
            }
        } else {
            System.out.println("Valor inválido para saque.");
        }
    }
}

--> Conta ContaInvestimentoAltoRisco
public class ContaInvestimentoAltoRisco extends ContaInvestimento {
    private static final double SALDO_MINIMO = 10000.00;

    public ContaInvestimentoAltoRisco(String numeroConta, String titular, double saldo) {
        super(numeroConta, titular, saldo);
    }

    @Override
    public void sacar(double valor) {
        double taxa = valor * 0.05;
        double valorFinal = valor + taxa;

        if (saldo >= SALDO_MINIMO && saldo >= valorFinal) {
            saldo -= valorFinal;
            System.out.println("Saque de R$" + valor + " realizado com taxa de R$" + taxa + ". Novo saldo: R$" + saldo);
        } else {
            System.out.println("Saque não permitido. Saldo abaixo do mínimo exigido de R$" + SALDO_MINIMO);
        }
    }
}

--> Conta Main
public class Main {
    public static void main(String[] args) {
        System.out.println("=== Conta Corrente ===");
        ContaCorrente cc = new ContaCorrente("12345", "João Silva", 1000, 500);
        cc.exibirInformacoes();
        cc.sacar(1300);
        cc.sacar(300);

        System.out.println("\n=== Conta Poupança ===");
        ContaPoupanca cp = new ContaPoupanca("54321", "Maria Oliveira", 2000);
        cp.exibirInformacoes();
        cp.sacar(2500); // Saldo insuficiente
        cp.sacar(1500);

        System.out.println("\n=== Conta Investimento ===");
        ContaInvestimento ci = new ContaInvestimento("67890", "Carlos Mendes", 5000);
        ci.exibirInformacoes();
        ci.sacar(1000);

        System.out.println("\n=== Conta Salário ===");
        ContaSalario cs = new ContaSalario("11223", "Ana Souza", 3000, 500);
        cs.exibirInformacoes();
        cs.sacar(1000); // Primeiro saque grátis
        cs.sacar(1000); // Segundo saque com taxa de R$5

        System.out.println("\n=== Conta Investimento Alto Risco ===");
        ContaInvestimentoAltoRisco cial = new ContaInvestimentoAltoRisco("44556", "Bruno Lima", 12000);
        cial.exibirInformacoes();
        cial.sacar(2000); // Taxa de 5%
        cial.sacar(8000); // Saldo abaixo do mínimo exigido
    }
}
