Armazenamento de Blobs do Azure é um serviço de baixo custo e dimensionável em massa, que pode ser utilizado para alojar ficheiros estáticos. Para este tutorial, deve utilizá-lo para servir conteúdo estático (por exemplo, HTML, JavaScript, CSS) para a aplicação Web que criar.

## <a name="create-a-storage-account"></a>Criar uma conta de Armazenamento

Uma conta de Armazenamento é um recurso do Azure que lhe permite armazenar tabelas, filas, ficheiros, blobs (objetos) e discos da máquina virtual.

1. Inicie sessão no Cloud Shell (Bash), selecionando o botão **Entrar no modo de detalhe**. Este botão está no canto superior direito ou na parte inferior da página, dependendo da largura da janela do seu browser. O modo de detalhe tem uma janela do Cloud Shell no lado direito da janela do browser, para que possa facilmente executar comandos que são apresentados no tutorial.

1. No Azure, um Grupo de Recursos é um contentor que retém recursos relacionados do Azure para facilitar a gestão. Crie um novo grupo de recursos chamado **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. O conteúdo estático (ficheiros HTML, CSS e JavaScript) para este tutorial está hospedado num Armazenamento de Blobs. O Armazenamento de Blobs requer uma conta de Armazenamento. Crie uma conta de Armazenamento (fins gerais V2) no grupo de recursos. Substitua `<storage account name>` por um nome exclusivo.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Com a barra de Pesquisa na parte superior do [portal do Azure](https://portal.azure.com), localize a conta de armazenamento que acabou de criar e abra-a.

1. No painel de navegação esquerdo, selecione **Site estático (pré-visualização)** para configurar um contentor para o alojamento do site estático.
    - Selecione **Ativado** para ativar o site estático.
    - Introduza *index.html* para o nome do documento de índice. O campo já inclui o texto *index.html* num tipo de letra cinzento, mas trata-se apenas de texto de exemplo e, como tal, tem ainda de introduzir esse valor no campo.
    - Clique em **Guardar**.
    
    ![Introduzir definições de site estático](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Guardar o **ponto final primário** num lugar onde possa facilmente copiá-lo enquanto trabalhar no tutorial. Este é o URL da sua aplicação Web.

## <a name="upload-the-web-application"></a>Carregar a aplicação Web

1. Os ficheiros de origem para a aplicação que criar neste tutorial estão localizados num [repositório do GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application). Certifique-se de que está no diretório raiz do Cloud Shell para clonar este repositório.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    O repositório for clonado para `/home/<username>/functions-first-serverless-web-application`.

1. A aplicação Web do lado do cliente está localizada na pasta **www** e foi criada usando a arquitetura Vue.js JavaScript. Mude para a pasta e execute os comandos npm para instalar as dependências da aplicação e criar a aplicação. O último desses comandos pode demorar vários minutos a concluir.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    A aplicação é gerada na pasta **dist**.

1. Altere o diretório atual para **dist** e carregue a aplicação para o contentor de Blobs **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Abra o URL de ponto final primário de sites estáticos de Armazenamento num browser para ver a aplicação.

    ![Primeira home page da aplicação Web sem servidor](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Resumo

Neste módulo, criou um grupo de recursos chamado **first-serverless-app** que contém uma conta de Armazenamento. Um contentor de Blobs denominado **$web** na conta de Armazenamento armazena o conteúdo estático da sua aplicação Web e torna o conteúdo disponível publicamente. Em seguida, irá aprender a utilizar uma função sem servidor que lhe permite carregar imagens para o armazenamento de Blobs a partir desta aplicação Web.
