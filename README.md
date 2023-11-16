# Roteiro Prático sobre Remote Method Invocation (RMI)
Roteiro prático sobre Remote Method Invocation (RMI) 

Este roteiro prático detalha o exemplo disponibilizado pela [Oracle](https://docs.oracle.com/javase/8/docs/technotes/guides/rmi/hello/hello-world.html), que apresenta as APIs para implementação de Java Remote Method Invocations (Java RMI).


> A interface de programação de aplicativos (API) Java Remote Method Invocation (RMI) permite comunicações de cliente e servidor pela rede. Normalmente, os programas clientes enviam solicitações a um programa servidor, e o programa servidor responde as solicitações. Um exemplo comum é o compartilhamento de um programa de processamento de texto em uma rede. O processador de texto é instalado em um servidor e quem quiser utilizá-lo inicia-o em sua máquina. A invocação envia uma solicitação a um programa servidor para acesso ao software, e o programa servidor responde disponibilizando o software ao solicitante. A API RMI permite criar um objeto de servidor remoto acessível publicamente, permitindo comunicações entre cliente e servidor por meio de chamadas de método simples no objeto de servidor. Os clientes podem se comunicar facilmente diretamente com o objeto servidor e indiretamente entre si por meio do objeto servidor usando Uniform Resource Locators (URLs) e HyperText Transfer Protocol (HTTP)." -- Adaptado, [Oracle, Remote Method Invocation](https://www.oracle.com/java/technologies/jpl1-remote-method-invocation.html)


# Parte 1: Interface Remota

O primeiro passo consiste em criar uma interface, que define as operações que serão realizadas remotamente. 

Crie duas pastas: `server` e `client`. Adicione a seguinte interface em ambas:

```java
import java.rmi.Remote;
import java.rmi.RemoteException;

public interface Hello extends Remote {
    String sayHello() throws RemoteException;
}
```

# Parte 2: Servidor e  objeto remoto

No lado do servidor, precisamos tornar as operações e objetos acessíveis via rede. Para tanto adicione a seguinte classe na pasta `server`:

```java
import java.rmi.registry.Registry;
import java.rmi.registry.LocateRegistry;
import java.rmi.RemoteException;
import java.rmi.server.UnicastRemoteObject;
        
public class Server implements Hello {
        
    public Server() {}

    public String sayHello() {
        return "Hello, world!";
    }
        
    public static void main(String args[]) {
        
        try {
            //Create and export a remote object
            Server obj = new Server();
            Hello stub = (Hello) UnicastRemoteObject.exportObject(obj, 0);

            //Register the remote object with a Java RMI registry
            Registry registry = LocateRegistry.getRegistry("127.0.0.1", 9400);
            registry.bind("Hello", stub);

            System.err.println("Server ready");
        } catch (Exception e) {
            System.err.println("Server exception: " + e.toString());
            e.printStackTrace();
        }
    }
}
```

Por padrão, registry é disponibilizado na porta 1099. Neste exemplo prático, estamos utilizando a porta 9400. Se quisermos utilizar a porta padrão, basta substituir o comando por `Registry registry = LocateRegistry.getRegistry();`. Em outras palavras, devemos omitir os parâmetros de configuração.

# Parte 3: Cliente

Por fim, basta implementar as chamadas no lado do cliente. Para tanto, adicione a seguinte classe na pasta client:

```java
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;

public class Client {

    private Client() {}

    public static void main(String[] args) {

        try {
            Registry registry = LocateRegistry.getRegistry("127.0.0.1", 9400);
            Hello stub = (Hello) registry.lookup("Hello");
            String response = stub.sayHello();
            System.out.println("response: " + response);
        } catch (Exception e) {
            System.err.println("Client exception: " + e.toString());
            e.printStackTrace();
        }
    }
}
```

# Parte 4: Execução


Primeiro, compile os arquivos do lado do cliente:

```
cd client
javac *.java
```

Na pasta `client``, inicie também o registry:

```
rmiregistry 9400 &
```

Em seguida, compile o código do lado do servidor e execute:

```
cd server
javac *.java
java Server
```

Execute uma chamada remota na pasta `client` e observe a saída:
```
java Client
```

# Parte 4: Entrega

Após executar e compreender o código, adapte o projeto acima para fazer uma calculadora via chamadas remotas. A calculadora deve receber dois operadores e realizar quatro operações básicas definidas na sua interface: adição, subtração, multiplicação, e divisão.

Entrega individual.

Submeter um relatório via Canvas, que inclui:

- Cabeçalho com o nome completo e matrícula.
- Link para o projeto no GitHub.
- Breve descrição da solução proposta.
- Casos de teste da calculadora, mostrando o console com as suas respectivas entradas e saídas.



# Referências


Oracle. Lesson 8: Remote Method Invocation. Disponível em: [https://www.oracle.com/java/technologies/jpl1-remote-method-invocation.html](https://www.oracle.com/java/technologies/jpl1-remote-method-invocation.html). Acesso em Novembro de 2023.


IBM. Java remote method invocation. Disponível em 
[https://www.ibm.com/docs/en/mfci/7.6.1?topic=server-java-remote-method-invocation](https://www.ibm.com/docs/en/mfci/7.6.1?topic=server-java-remote-method-invocation). Acesso em Novembro de 2023.


