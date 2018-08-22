<span data-ttu-id="786ea-101">Armazenamento de Blobs do Azure é um serviço de baixo custo e dimensionável em massa, que pode ser utilizado para alojar ficheiros estáticos.</span><span class="sxs-lookup"><span data-stu-id="786ea-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="786ea-102">Para este tutorial, deve utilizá-lo para servir conteúdo estático (por exemplo, HTML, JavaScript, CSS) para a aplicação Web que criar.</span><span class="sxs-lookup"><span data-stu-id="786ea-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="786ea-103">Criar uma conta de Armazenamento</span><span class="sxs-lookup"><span data-stu-id="786ea-103">Create a Storage account</span></span>

<span data-ttu-id="786ea-104">Uma conta de Armazenamento é um recurso do Azure que lhe permite armazenar tabelas, filas, ficheiros, blobs (objetos) e discos da máquina virtual.</span><span class="sxs-lookup"><span data-stu-id="786ea-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="786ea-105">Inicie sessão no Cloud Shell (Bash), selecionando o botão **Entrar no modo de detalhe**.</span><span class="sxs-lookup"><span data-stu-id="786ea-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="786ea-106">Este botão está no canto superior direito ou na parte inferior da página, dependendo da largura da janela do seu browser.</span><span class="sxs-lookup"><span data-stu-id="786ea-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="786ea-107">O modo de detalhe tem uma janela do Cloud Shell no lado direito da janela do browser, para que possa facilmente executar comandos que são apresentados no tutorial.</span><span class="sxs-lookup"><span data-stu-id="786ea-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="786ea-108">No Azure, um Grupo de Recursos é um contentor que retém recursos relacionados do Azure para facilitar a gestão.</span><span class="sxs-lookup"><span data-stu-id="786ea-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="786ea-109">Crie um novo grupo de recursos chamado **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="786ea-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="786ea-110">O conteúdo estático (ficheiros HTML, CSS e JavaScript) para este tutorial está hospedado num Armazenamento de Blobs.</span><span class="sxs-lookup"><span data-stu-id="786ea-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="786ea-111">O Armazenamento de Blobs requer uma conta de Armazenamento.</span><span class="sxs-lookup"><span data-stu-id="786ea-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="786ea-112">Crie uma conta de Armazenamento (fins gerais V2) no grupo de recursos.</span><span class="sxs-lookup"><span data-stu-id="786ea-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="786ea-113">Substitua `<storage account name>` por um nome exclusivo.</span><span class="sxs-lookup"><span data-stu-id="786ea-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="786ea-114">Com a barra de Pesquisa na parte superior do [portal do Azure](https://portal.azure.com), localize a conta de armazenamento que acabou de criar e abra-a.</span><span class="sxs-lookup"><span data-stu-id="786ea-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="786ea-115">No painel de navegação esquerdo, selecione **Site estático (pré-visualização)** para configurar um contentor para o alojamento do site estático.</span><span class="sxs-lookup"><span data-stu-id="786ea-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="786ea-116">Selecione **Ativado** para ativar o site estático.</span><span class="sxs-lookup"><span data-stu-id="786ea-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="786ea-117">Introduza *index.html* para o nome do documento de índice.</span><span class="sxs-lookup"><span data-stu-id="786ea-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="786ea-118">O campo já inclui o texto *index.html* num tipo de letra cinzento, mas trata-se apenas de texto de exemplo e, como tal, tem ainda de introduzir esse valor no campo.</span><span class="sxs-lookup"><span data-stu-id="786ea-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="786ea-119">Clique em **Guardar**.</span><span class="sxs-lookup"><span data-stu-id="786ea-119">Click **Save**.</span></span>
    
    ![Introduzir definições de site estático](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="786ea-121">Guardar o **ponto final primário** num lugar onde possa facilmente copiá-lo enquanto trabalhar no tutorial.</span><span class="sxs-lookup"><span data-stu-id="786ea-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="786ea-122">Este é o URL da sua aplicação Web.</span><span class="sxs-lookup"><span data-stu-id="786ea-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="786ea-123">Carregar a aplicação Web</span><span class="sxs-lookup"><span data-stu-id="786ea-123">Upload the web application</span></span>

1. <span data-ttu-id="786ea-124">Os ficheiros de origem para a aplicação que criar neste tutorial estão localizados num [repositório do GitHub](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="786ea-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="786ea-125">Certifique-se de que está no diretório raiz do Cloud Shell para clonar este repositório.</span><span class="sxs-lookup"><span data-stu-id="786ea-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="786ea-126">O repositório for clonado para `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="786ea-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="786ea-127">A aplicação Web do lado do cliente está localizada na pasta **www** e foi criada usando a arquitetura Vue.js JavaScript.</span><span class="sxs-lookup"><span data-stu-id="786ea-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="786ea-128">Mude para a pasta e execute os comandos npm para instalar as dependências da aplicação e criar a aplicação.</span><span class="sxs-lookup"><span data-stu-id="786ea-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="786ea-129">O último desses comandos pode demorar vários minutos a concluir.</span><span class="sxs-lookup"><span data-stu-id="786ea-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="786ea-130">A aplicação é gerada na pasta **dist**.</span><span class="sxs-lookup"><span data-stu-id="786ea-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="786ea-131">Altere o diretório atual para **dist** e carregue a aplicação para o contentor de Blobs **$web**.</span><span class="sxs-lookup"><span data-stu-id="786ea-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="786ea-132">Abra o URL de ponto final primário de sites estáticos de Armazenamento num browser para ver a aplicação.</span><span class="sxs-lookup"><span data-stu-id="786ea-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Primeira home page da aplicação Web sem servidor](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="786ea-134">Resumo</span><span class="sxs-lookup"><span data-stu-id="786ea-134">Summary</span></span>

<span data-ttu-id="786ea-135">Neste módulo, criou um grupo de recursos chamado **first-serverless-app** que contém uma conta de Armazenamento.</span><span class="sxs-lookup"><span data-stu-id="786ea-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="786ea-136">Um contentor de Blobs denominado **$web** na conta de Armazenamento armazena o conteúdo estático da sua aplicação Web e torna o conteúdo disponível publicamente.</span><span class="sxs-lookup"><span data-stu-id="786ea-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="786ea-137">Em seguida, irá aprender a utilizar uma função sem servidor que lhe permite carregar imagens para o armazenamento de Blobs a partir desta aplicação Web.</span><span class="sxs-lookup"><span data-stu-id="786ea-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
