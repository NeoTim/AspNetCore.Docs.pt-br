---
title: Introdução ao ASP.NET Core mais incrivelmente
author: guardrex
description: Comece com o mais elaborado criando um aplicativo mais incrivelmente com as ferramentas de sua escolha.
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/get-started
ms.openlocfilehash: 1358a2e92af9d9104e565718692b1ca1940b9d9e
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2019
ms.locfileid: "68993399"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="8945f-103">Introdução ao ASP.NET Core mais incrivelmente</span><span class="sxs-lookup"><span data-stu-id="8945f-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="8945f-104">Por [Daniel Roth](https://github.com/danroth27) e [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8945f-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8945f-105">Introdução ao mais incrivelmente:</span><span class="sxs-lookup"><span data-stu-id="8945f-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="8945f-106">Instale a versão mais recente do [SDK do .NET Core 3,0 Preview](https://dotnet.microsoft.com/download/dotnet-core/3.0) .</span><span class="sxs-lookup"><span data-stu-id="8945f-106">Install the latest [.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="8945f-107">Instale os modelos mais poficados executando o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8945f-107">Install the Blazor templates by running the following command in a command shell:</span></span>

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview8.19405.7
   ```

1. <span data-ttu-id="8945f-108">Siga as orientações para sua escolha de ferramentas:</span><span class="sxs-lookup"><span data-stu-id="8945f-108">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8945f-109">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8945f-109">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="8945f-110">1 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-110">1\.</span></span> <span data-ttu-id="8945f-111">Instale a visualização mais recente do [Visual Studio](https://visualstudio.com/vs/preview) com a ASP.net e a carga de trabalho de **desenvolvimento Web** .</span><span class="sxs-lookup"><span data-stu-id="8945f-111">Install the latest [Visual Studio preview](https://visualstudio.com/vs/preview) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="8945f-112">2 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-112">2\.</span></span> <span data-ttu-id="8945f-113">Crie um novo projeto.</span><span class="sxs-lookup"><span data-stu-id="8945f-113">Create a new project.</span></span>

   <span data-ttu-id="8945f-114">3 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-114">3\.</span></span> <span data-ttu-id="8945f-115">Selecione **aplicativo mais incrivelmente**.</span><span class="sxs-lookup"><span data-stu-id="8945f-115">Select **Blazor App**.</span></span> <span data-ttu-id="8945f-116">Selecione **Avançar**.</span><span class="sxs-lookup"><span data-stu-id="8945f-116">Select **Next**.</span></span>

   <span data-ttu-id="8945f-117">4 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-117">4\.</span></span> <span data-ttu-id="8945f-118">Forneça um nome ao projeto no campo **Nome do projeto** ou aceite o nome do projeto padrão.</span><span class="sxs-lookup"><span data-stu-id="8945f-118">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="8945f-119">Confirme se a entrada de **local** está correta ou forneça um local para o projeto.</span><span class="sxs-lookup"><span data-stu-id="8945f-119">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8945f-120">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="8945f-120">Select **Create**.</span></span>

   <span data-ttu-id="8945f-121">5 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-121">5\.</span></span> <span data-ttu-id="8945f-122">Para uma experiência mais experiente no lado do cliente, escolha o modelo de **aplicativo Webassembly** mais experiente.</span><span class="sxs-lookup"><span data-stu-id="8945f-122">For a Blazor client-side experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="8945f-123">Para uma experiência mais experiente no lado do servidor, escolha o modelo de **aplicativo de servidor mais incrivelmente** .</span><span class="sxs-lookup"><span data-stu-id="8945f-123">For a Blazor server-side experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="8945f-124">Selecione **Criar**.</span><span class="sxs-lookup"><span data-stu-id="8945f-124">Select **Create**.</span></span> <span data-ttu-id="8945f-125">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, no lado do servidor e no lado <xref:blazor/hosting-models>do cliente, consulte.</span><span class="sxs-lookup"><span data-stu-id="8945f-125">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8945f-126">6 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-126">6\.</span></span> <span data-ttu-id="8945f-127">Pressione **F5** para executar o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8945f-127">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="8945f-128">Se você instalou a extensão do Visual Studio para uma versão prévia anterior do ASP.NET Core mais recente (visualização 6 ou anterior), você pode desinstalar a extensão.</span><span class="sxs-lookup"><span data-stu-id="8945f-128">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="8945f-129">Instalar os modelos mais bem em um shell de comando agora é suficiente para trazer os modelos no Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="8945f-129">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8945f-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8945f-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="8945f-131">1 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-131">1\.</span></span> <span data-ttu-id="8945f-132">Instalar o [Visual Studio Code](https://code.visualstudio.com/).</span><span class="sxs-lookup"><span data-stu-id="8945f-132">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="8945f-133">2 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-133">2\.</span></span> <span data-ttu-id="8945f-134">Instale o mais recente [ C# para a extensão de Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span><span class="sxs-lookup"><span data-stu-id="8945f-134">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="8945f-135">3 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-135">3\.</span></span> <span data-ttu-id="8945f-136">Para uma experiência mais experiente no lado do cliente, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8945f-136">For a Blazor client-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="8945f-137">Para uma experiência mais experiente no lado do servidor, execute o seguinte comando em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8945f-137">For a Blazor server-side experience, execute the following command in a command shell:</span></span>

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="8945f-138">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, no lado do servidor e no lado <xref:blazor/hosting-models>do cliente, consulte.</span><span class="sxs-lookup"><span data-stu-id="8945f-138">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8945f-139">4 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-139">4\.</span></span> <span data-ttu-id="8945f-140">Abra a pasta *WebApplication1* em Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8945f-140">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="8945f-141">5 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-141">5\.</span></span> <span data-ttu-id="8945f-142">Para um projeto do lado do servidor mais valioso, o IDE solicita que você adicione ativos para compilar e depurar o projeto.</span><span class="sxs-lookup"><span data-stu-id="8945f-142">For a Blazor server-side project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="8945f-143">Selecione **Sim** na barra superior.</span><span class="sxs-lookup"><span data-stu-id="8945f-143">Select **Yes**.</span></span>

   <span data-ttu-id="8945f-144">6 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-144">6\.</span></span> <span data-ttu-id="8945f-145">Se estiver usando um aplicativo do lado do servidor mais alto, execute o aplicativo usando o depurador de Visual Studio Code.</span><span class="sxs-lookup"><span data-stu-id="8945f-145">If using a Blazor server-side app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="8945f-146">Se estiver usando um aplicativo do lado do cliente mais alto `dotnet run` , execute na pasta do projeto do aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8945f-146">If using a Blazor client-side app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="8945f-147">7 \.</span><span class="sxs-lookup"><span data-stu-id="8945f-147">7\.</span></span> <span data-ttu-id="8945f-148">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8945f-148">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor server-side experience, select the **Blazor Server App** template. For a Blazor client-side experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="8945f-149">CLI do .NET Core</span><span class="sxs-lookup"><span data-stu-id="8945f-149">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="8945f-150">Para uma experiência mais experiente no lado do cliente, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8945f-150">For a Blazor client-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8945f-151">Para uma experiência mais experiente no lado do servidor, execute os seguintes comandos em um shell de comando:</span><span class="sxs-lookup"><span data-stu-id="8945f-151">For a Blazor server-side experience, execute the following commands in a command shell:</span></span>

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="8945f-152">Para obter informações sobre os dois modelos de hospedagem mais incrivelmente, no lado do servidor e no lado <xref:blazor/hosting-models>do cliente, consulte.</span><span class="sxs-lookup"><span data-stu-id="8945f-152">For information on the two Blazor hosting models, server-side and client-side, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="8945f-153">Em um navegador, navegue até `https://localhost:5001`.</span><span class="sxs-lookup"><span data-stu-id="8945f-153">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="8945f-154">Várias páginas estão disponíveis em guias na barra lateral:</span><span class="sxs-lookup"><span data-stu-id="8945f-154">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="8945f-155">Home</span><span class="sxs-lookup"><span data-stu-id="8945f-155">Home</span></span>
* <span data-ttu-id="8945f-156">Contador</span><span class="sxs-lookup"><span data-stu-id="8945f-156">Counter</span></span>
* <span data-ttu-id="8945f-157">Buscar dados</span><span class="sxs-lookup"><span data-stu-id="8945f-157">Fetch data</span></span>

<span data-ttu-id="8945f-158">Na página Contador, selecione o botão **Clique aqui** para incrementar o contador sem uma atualização de página.</span><span class="sxs-lookup"><span data-stu-id="8945f-158">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="8945f-159">Incrementar um contador em uma página da Web normalmente requer a escrita de JavaScript, mas os componentes do Razor C#fornecem uma abordagem melhor usando o.</span><span class="sxs-lookup"><span data-stu-id="8945f-159">Incrementing a counter in a webpage normally requires writing JavaScript, but Razor components provide a better approach using C#.</span></span>

<span data-ttu-id="8945f-160">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="8945f-160">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="8945f-161">Uma solicitação para `/counter` no navegador, conforme especificado `@page` pela diretiva na parte superior, faz com que o `Counter` componente processe seu conteúdo.</span><span class="sxs-lookup"><span data-stu-id="8945f-161">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="8945f-162">Os componentes são renderizados em uma representação na memória da árvore de renderização que pode ser usada para atualizar a interface do usuário de maneira flexível e eficiente.</span><span class="sxs-lookup"><span data-stu-id="8945f-162">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="8945f-163">Sempre que o botão **clicar em mim** estiver selecionado:</span><span class="sxs-lookup"><span data-stu-id="8945f-163">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="8945f-164">O `onclick` evento é acionado.</span><span class="sxs-lookup"><span data-stu-id="8945f-164">The `onclick` event is fired.</span></span>
* <span data-ttu-id="8945f-165">O método `IncrementCount` é chamado.</span><span class="sxs-lookup"><span data-stu-id="8945f-165">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="8945f-166">O `currentCount` é incrementado.</span><span class="sxs-lookup"><span data-stu-id="8945f-166">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="8945f-167">O componente é renderizado novamente.</span><span class="sxs-lookup"><span data-stu-id="8945f-167">The component is rendered again.</span></span>

<span data-ttu-id="8945f-168">O tempo de execução compara o novo conteúdo com o conteúdo anterior e aplica apenas o conteúdo alterado para o Modelo de Objeto do Documento (DOM).</span><span class="sxs-lookup"><span data-stu-id="8945f-168">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="8945f-169">Adicione um componente a outro componente usando a sintaxe HTML.</span><span class="sxs-lookup"><span data-stu-id="8945f-169">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="8945f-170">Por exemplo, adicione o `Counter` componente à página inicial do aplicativo adicionando um `<Counter />` elemento ao `Index` componente.</span><span class="sxs-lookup"><span data-stu-id="8945f-170">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="8945f-171">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="8945f-171">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="8945f-172">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8945f-172">Run the app.</span></span> <span data-ttu-id="8945f-173">A Home Page tem seu próprio contador fornecido pelo `Counter` componente.</span><span class="sxs-lookup"><span data-stu-id="8945f-173">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="8945f-174">Os parâmetros de componente são especificados usando atributos ou [conteúdo filho](xref:blazor/components#child-content), que permitem definir propriedades no componente filho.</span><span class="sxs-lookup"><span data-stu-id="8945f-174">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="8945f-175">Para adicionar um parâmetro ao `Counter` componente, atualize o bloco do `@code` componente:</span><span class="sxs-lookup"><span data-stu-id="8945f-175">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="8945f-176">Adicione uma propriedade pública para `IncrementAmount` com um `[Parameter]` atributo.</span><span class="sxs-lookup"><span data-stu-id="8945f-176">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="8945f-177">Altere o método `IncrementCount` para usar o `IncrementAmount` ao aumentar o valor de `currentCount`.</span><span class="sxs-lookup"><span data-stu-id="8945f-177">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="8945f-178">*Pages/Counter.razor*:</span><span class="sxs-lookup"><span data-stu-id="8945f-178">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="8945f-179">Especifique o `IncrementAmount` noelemento`<Counter>` do componenteusandoumatributo.`Index`</span><span class="sxs-lookup"><span data-stu-id="8945f-179">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="8945f-180">*Pages/Index.razor*:</span><span class="sxs-lookup"><span data-stu-id="8945f-180">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="8945f-181">Execute o aplicativo.</span><span class="sxs-lookup"><span data-stu-id="8945f-181">Run the app.</span></span> <span data-ttu-id="8945f-182">O `Index` componente tem seu próprio contador que é incrementado em dez cada vez que o botão **Click me** está selecionado.</span><span class="sxs-lookup"><span data-stu-id="8945f-182">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="8945f-183">O `Counter` componente (*Counter. Razor*) em `/counter` continua a incrementar um.</span><span class="sxs-lookup"><span data-stu-id="8945f-183">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8945f-184">Próximas etapas</span><span class="sxs-lookup"><span data-stu-id="8945f-184">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="8945f-185">Recursos adicionais</span><span class="sxs-lookup"><span data-stu-id="8945f-185">Additional resources</span></span>

* <xref:signalr/introduction>