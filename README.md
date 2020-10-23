# ServerlessApplication
 
Documentação com imagens: https://docs.google.com/document/d/1O0jsUPNvpqVCBmmY62LOGuF8q0iXDGmshwX85PV09aw/edit?usp=sharing

Para fazer uma coisa diferente decidi ao invés de subir um docker com uma API, decidi subir uma aplicação Serverless, ou seja uma lambda function, que no caso é considerado uma “evolução” do docker, Serverless remete a sem servidor, e com ele vêm o termo de Backend as a Service, que utiliza dos containers/docker para subir instâncias sem que precisemos nos preocupar com a parte de infra, e nem com o escalonamento. Irei subir na AWS, com auxílio do visual studio para gerar os templates.

A medida que for explicando o passo a passo, irei explicando melhor como funciona.

O pessoal da AWS criou uma extensão para o visual studio para permitir criação rápida de funções lambdas, então, vamos utilizá-la para mostrar como é fácil subir uma aplicação.

Primeiro vamos instalar a extensão, basta ir em Extensions e depois em Manage Extensions:

Irá abrir uma janela do gerenciador de extensões, basta procurar por AWS que será a primeira: 

Após instalar, vamos criar um projeto em .net core, utilizando o tipo AWS Serverless Application


Após selecionar para criar o projeto e dizer o nome, ele irá perguntar qual tipo de conteúdo deseja criar, vamos em Asp.net Core Web Api para que depois de subirmos termos um hello world :)


Após criar o projeto, alguns arquivos vão ser criados, e o ponto principal é o serverless.template, ele funciona parecido com o DockerFile, é onde iremos dizer onde como irá subir nossa aplicação e também suas configurações, por exemplo, podemos definir quantas instâncias podem ser criadas para atender a esse serviço em específico.

Explicando o funcionamento de uma forma resumida, assim que subirmos o lambda, o código será comprimido e enviado para um bucket no s3, em seguida iremos ver como configurar isso, e é de lá que a instância irá pegar o código para executar. E se você está pensando em micro serviços, sim, esse é o princípio, iremos gerar um serviço dinâmico com esse lambda.
Além do template, temos também os entry points que é onde definimos como será iniciada a nossa aplicação, para quem trabalha com c#, é bem similar ao startup.cs e o program.cs.
Chega de enrolação, vamos começar a subir o nosso lambda, para iniciar o processo vamos clicar com o botão direito no projeto e ir na opção “Publish to AWS Lambda”

Quando clicarmos nisso irá abrir a tela de configuração de conta, para configurar uma conta é fácil, precisamos pegar o usuário da AWS que iremos utilizar com suas configurações que é exportado com csv e depois importar que ele já irá identificar os dados.
Para pegar os dados do usuário basta ir na opção de IAM e depois em usuários e pegar algum existente ou criar um novo, precisaremos de algumas permissões para subir o lambda, para nível didático eu utilizei o AdministratorAccess para facilitar.

Após inserir os dados, ele irá perguntar qual bucket quer utilizar e qual a stack, tudo que estamos fazendo será adicionado a uma stack, ai podemos agrupar por ela.


Após fazer isso basta dar next e publish que o serviço irá começar a ser levantado.
Após o serviço ser levantado ele irá exibir essa tela, já com o link do lambda:


O Lambda foi instanciado apontando para essa URL, e como subimos uma API já vem com um controller padrão, para visualizar se está funcionando copie a url e adicione “api/values” no final que irá chamar o controller de values que foi criado.

Esse é o resultado


Agora temos uma aplicação rodando em serverless, no final das contas ainda existe sim um servidor por baixo que é levantado a instância a medida que recebermos requisição, se não tiver requisição em uma faixa de em torno de 4 minutos ele é derrubado e na próxima requisição já é levantado automaticamente.

Após subir o lambda podemos configurar mais coisas, como por exemplo, o máximo de memória que será utilizado e o tempo de timeout


Além disso quando o lambda sobe automaticamente é criado um streaming com o CloudWatch para monitorar os recursos, e então é feito toda uma monitoração das suas instâncias, lembrando que como é levantado várias instâncias para um único serviço a monitoração é geral.

Para ver a monitoração basta ir na aba “Monitoramento” dentro do Lambda:

