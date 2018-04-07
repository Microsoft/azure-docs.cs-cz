---
title: Vytvoření kanálu CI/CD v Azure pomocí Team Services | Microsoft Docs
description: Naučte se vytvořit kanál Visual Studio Team Services pro průběžnou integraci a doručení, které nasadí webové aplikace do služby IIS na virtuální počítač s Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/12/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: cf6e3013d4dfc7e18d96a717a76b591cde939139
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Vytvoření kanálu průběžnou integraci s Visual Studio Team Services a služby IIS
K automatizaci sestavení, testování a fáze nasazení pro vývoj aplikací, můžete použít průběžnou integraci a nasazení (CI/CD) kanálu. V tomto kurzu vytvoříte kanál CI/CD pomocí Visual Studio Team Services a virtuálního počítače (VM) s Windows v Azure, který spouští IIS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Publikovat webovou aplikaci ASP.NET do projektu Team Services
> * Vytvořit definici sestavení, která se spustí při potvrzení kódu
> * Instalace a konfigurace služby IIS na virtuální počítač v Azure
> * Instance služby IIS přidat do skupiny nasazení v Team Services
> * Vytvořit verzi definice k publikování nové webové nasazení balíčků do služby IIS
> * Testování CI/CD kanálu

Tento kurz vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Vytvoření projektu v Team Services
Visual Studio Team Services umožňuje snadno spolupráce a vývoj bez zachování do řešení pro správu kódu na místě. Team Services poskytuje testování kódu cloudu, sestavení a službu application insights. Můžete IDE, které nejlépe vyhovuje vaší vývoj kódu a verzi ovládací prvek úložiště. V tomto kurzu slouží k vytvoření základní webové aplikace ASP.NET a CI/CD kanálu bezplatný účet. Pokud jste již nemají účet Team Services [vytvořit](http://go.microsoft.com/fwlink/?LinkId=307137).

Spravovat proces potvrzení kódu, definice sestavení a verze definice, vytvoření projektu v Team Services následujícím způsobem:

1. Otevřete řídicí panel Team Services ve webovém prohlížeči a zvolte **nový projekt**.
2. Zadejte *myWebApp* pro **název projektu**. Nechte všechny ostatní výchozí hodnoty pro použití *Git* verzí a *Agile* zpracování pracovní položky.
3. Zvolte možnost **sdílet s** *členové týmu*, pak vyberte **vytvořit**.
5. Po vytvoření projektu, zvolte možnost **inicializovat README nebo gitignore**, pak **inicializovat**.
6. Uvnitř nový projekt, vyberte **řídicí panely** v horní části, pak vyberte **otevřete v sadě Visual Studio**.


## <a name="create-aspnet-web-application"></a>Vytvoření webové aplikace ASP.NET
V předchozím kroku jste vytvořili projektu v Team Services. V posledním kroku otevře nový projekt v sadě Visual Studio. Spravovat vaše potvrzení kódu v **Team Explorer** okno. Vytvořit místní kopii nový projekt, a poté vytvořit webovou aplikaci ASP.NET ze šablony takto:

1. Vyberte **klon** k vytvoření úložiště místní git projektu Team Services.
    
    ![Klonování úložiště z projektu Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. V části **řešení**, vyberte **nový**.

    ![Vytvoření řešení webové aplikace](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Vyberte **webové** šablony a potom zvolte **webové aplikace ASP.NET** šablony.
    1. Zadejte název vaší aplikace, jako například *myWebApp*a zrušte zaškrtnutí políčka pro **vytvořit adresář pro řešení**.
    2. Pokud možnost je dostupná, zrušte zaškrtnutí políčka **přidat službu Application Insights do projektu**. Application Insights, musíte k autorizaci webové aplikace pomocí služby Azure Application Insights. Chcete-li zachovat jednoduché v tomto kurzu, přeskočte tento proces.
    3. Vyberte **OK**.
4. Zvolte **MVC** ze seznamu šablony.
    1. Vyberte **změna ověřování**, zvolte **bez ověřování**, pak vyberte **OK**.
    2. Vyberte **OK** k vytvoření řešení.
5. V **Team Explorer** okně zvolte **změny**.

    ![Potvrzení změn místní úložiště git Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. V textovém poli potvrzení, zadejte zprávu *počáteční potvrzení*. Zvolte **potvrdit všechny a synchronizace** z rozevírací nabídky.


## <a name="create-build-definition"></a>Vytvořit definici sestavení
V Team Services je možné popisují, jak by měly být vytvořeny vaší aplikace pomocí definice sestavení. V tomto kurzu vytvoříme základní definice, který trvá naše zdrojového kódu sestavení řešení a potom vytvoří web nasadit balíček, který jsme můžete použít ke spuštění webové aplikace na serveru se službou IIS.

1. V projektu Team Services, zvolte **sestavení a verze** v horní části, pak vyberte **sestavení**.
3. Vyberte **+ novou definici**.
4. Vyberte **ASP.NET (PREVIEW)** šablony a vyberte **použít**.
5. Ponechejte všechny výchozí hodnoty úkolů. V části **získat zdroje**, ujistěte se, že *myWebApp* úložiště a *hlavní* jsou vybrat větev.

    ![Vytvořit definici sestavení v projektu Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na **aktivační události** kartě, přesuňte jezdec pro **povolení této aktivační události** k *povoleno*.
7. Uložit definici sestavení a fronty nového sestavení tak, že vyberete **Uložit & fronty** , pak **Uložit & fronty** znovu. Ponechte výchozí nastavení a vyberte **fronty**.

Kukátko jako sestavení je naplánováno na hostovaného agenta, pak začne sestavení. Výstup se podobá následujícímu příkladu:

![Úspěšné sestavení projektu Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Pokud chcete zadat platformu pro spouštění vaší webové aplikace ASP.NET, je nutné virtuálního počítače s Windows, který spouští IIS. Team Services používá k interakci s instancí služby IIS jako potvrdíte nějaký kód a aktivaci sestavení agenta.

Vytvoření virtuálního počítače Windows serveru 2016 pomocí [tento ukázkový skript](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). Jak dlouho trvá několik minut, než skript, který chcete spustit a vytvořit virtuální počítač. Po vytvoření virtuálního počítače, otevřete port 80 pro webový provoz s [přidat AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.resources/new-azurermresourcegroup) následujícím způsobem:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Chcete-li připojit k virtuálnímu počítači, získat veřejnou IP adresu s [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) následujícím způsobem:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Vytvořte relaci vzdálené plochy k virtuálnímu počítači:

```cmd
mstsc /v:<publicIpAddress>
```

Na virtuální počítač, otevřete **Powershellu správce** příkazového řádku. Instalace IIS a požadované funkce .NET následujícím způsobem:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Vytvoření skupiny nasazení
K nabízení balíček nasazení webu pro server služby IIS, můžete definovat skupiny nasazení v Team Services. Tato skupina umožňuje zadat, serverů, které jsou cílem nových sestavení automaticky jako potvrdíte nějaký kód do Team Services a sestavení se dokončí.

1. V Team Services, zvolte **sestavení a verze** a pak vyberte **nasazení skupiny**.
2. Zvolte **skupiny přidat nasazení**.
3. Zadejte název skupiny, jako například *mojeiis*, pak vyberte **vytvořit**.
4. V **registraci počítačů** se ujistěte, *Windows* je vybrána, a zaškrtněte políčko **používat osobní přístupový token ve skriptu pro ověřování**.
5. Vyberte **zkopírujte skript do schránky**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Přidat do skupiny nasazení virtuálních počítačů služby IIS
Ke skupině nasazení vytvořen přidejte do skupiny každá instance služby IIS. Team Services vytváří skript, který se stáhne a nakonfiguruje agenta na virtuálním počítači, který obdrží nové webové nasazení balíčků a použije ji do služby IIS.

1. Zpět v **Powershellu správce** relaci na vašem virtuálním počítači, vložte a spusťte skript zkopírovali z Team Services.
2. Po zobrazení výzvy ke konfiguraci značky pro agenta, vyberte *Y* a zadejte *webové*.
3. Po zobrazení výzvy pro uživatelský účet, stiskněte klávesu *vrátit* přijměte výchozí hodnoty.
4. Počkejte na dokončení zprávou skriptu *vstsagent.account.computername služby byla úspěšně spuštěna*.
5. V **nasazení skupiny** stránky **sestavení a verze** nabídky, otevřete *mojeiis* skupiny nasazení. Na **počítače** kartě, zkontrolujte, zda virtuální počítač.

    ![Virtuální počítač se úspěšně přidal do skupiny nasazení Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Vytvoření definice verze
K publikování buildy, můžete vytvořit definici verze v Team Services. Tuto definici se automaticky spustí podle úspěšném sestavení vaší aplikace. Zvolte skupinu nasazení tak, aby nabízel webového nasazení balíčku a zadejte odpovídající nastavení služby IIS.

1. Zvolte **sestavení a verze**, pak vyberte **sestavení**. Zvolte definici sestavení vytvořené v předchozím kroku.
2. V části **nedávném dokončení**, zvolte poslední sestavení a pak vyberte **verze**.
3. Zvolte **Ano** k vytvoření definice verzí.
4. Vyberte **prázdný** šablony, vyberte **Další**.
5. Zkontrolujte definici sestavení projektu a zdroj se naplní projektu.
6. Vyberte **průběžné nasazování** zaškrtněte políčko a potom vyberte **vytvořit**.
7. Vyberte pole rozevíracího seznamu vedle **+ přidat úlohy** a zvolte *přidat skupiny fáze nasazení*.
    
    ![Přidat úloha uvolnění definice v Team Services](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Zvolte **přidat** vedle **Deploy(Preview) aplikace webové služby IIS**, pak vyberte **Zavřít**.
9. Vyberte **spustit na skupiny nasazení** nadřazené úlohy.
    1. Pro **skupiny nasazení**, vyberte nasazení skupinu, kterou jste vytvořili dříve, jako například *mojeiis*.
    2. V **počítač značky** vyberte **přidat** a zvolte *webové* značky.
    
    ![Verze definice nasazení skupiny úloh pro službu IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Vyberte **nasadit: nasazení aplikace služby IIS webu** úlohy konfigurace instance nastavení služby IIS následujícím způsobem:
    1. Pro **název webu**, zadejte *Default Web Site*.
    2. Ponechte všechna ostatní výchozí nastavení.
12. Zvolte **Uložit**, pak vyberte **OK** dvakrát.


## <a name="create-a-release-and-publish"></a>Vytvoření vydání a publikování
Nyní můžete posouvat webového nasazení balíčku jako novou verzi. Tento krok komunikuje s agentem na každou instanci, která je součástí skupiny nasazení, nabízených oznámení webu nasazení balíčku a nakonfiguruje službu IIS ke spuštění aktualizované webové aplikace.

1. Ve vaší verzi definice, vyberte **+ verze**, zvolte **vytvořit verzi**.
2. Ověřte, zda je na nejnovější verzi vybrali v rozevíracím seznamu, spolu s **automatizované nasazení: Po vytvoření verze**. Vyberte **Vytvořit**.
3. Malé hlavička se zobrazí v horní části vaší definice vydání, například *vytvořil vydání verze 1*. Vyberte verzi odkaz.
4. Otevřete **protokoly** a sledovat průběh verze.
    
    ![Úspěšné verze Team Services a webové nasadit balíček push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Po dokončení verze otevřete webový prohlížeč a zadejte veřejnou adresu RP vašeho virtuálního počítače. Je spuštěna webová aplikace ASP.NET.

    ![Webová aplikace ASP.NET spuštěná na virtuálním počítači služby IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Testování celého kanálu CI/CD
S vaší webové aplikace běžící v IIS zkuste teď celého kanálu CI/CD. Po provedení změny v sadě Visual Studio a potvrzení spuštění kódu, sestavení, který potom aktivuje verzi aktualizované webového nasazení balíčku do služby IIS:

1. V sadě Visual Studio, otevřete **Průzkumníku řešení** okno.
2. Přejděte do a otevřete *myWebApp | Zobrazení | Domů | Index.cshtml*
3. Upravte řádek 6 ke čtení:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Uložte soubor.
5. Otevřete **Team Explorer** vyberte *myWebApp* projektu, a potom vyberte **změny**.
6. Zadejte zprávu o potvrzení, jako například *testování CI/CD kanálu*, zvolte **potvrzení všechny a synchronizace** z rozevírací nabídky.
7. V pracovním prostoru Team Services aktivuje se z kódu potvrzení nového sestavení. 
    - Zvolte **sestavení a verze**, pak vyberte **sestavení**. 
    - Vyberte svou definici sestavení a pak vyberte **zařazeno ve frontě & spuštěná** sestavení můžete sledovat v průběhu sestavení.
9. Po úspěšném sestavení se aktivuje novou verzi.
    - Zvolte **sestavení a verze**, pak **verze** zobrazíte webu nasazení balíčku nabídnutých do virtuálního počítače služby IIS. 
    - Vyberte **aktualizovat** ikonu Aktualizovat stav. Když *prostředí* sloupci se zobrazuje zelená značka zaškrtnutí, vydání úspěšně nasadil do služby IIS.
11. Chcete-li zobrazit změny použity, aktualizujte váš web služby IIS v prohlížeči.

    ![Spuštění virtuálního počítače služby IIS z CI/CD kanálu webové aplikace ASP.NET](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu vytvoříte webovou aplikaci ASP.NET ve službě Team Services a nakonfigurujete sestavení a definice vydání k nasazení nové webové nasazení balíčků do služby IIS na každém potvrzení kódu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Publikovat webovou aplikaci ASP.NET do projektu Team Services
> * Vytvořit definici sestavení, která se spustí při potvrzení kódu
> * Instalace a konfigurace služby IIS na virtuální počítač v Azure
> * Instance služby IIS přidat do skupiny nasazení v Team Services
> * Vytvořit verzi definice k publikování nové webové nasazení balíčků do služby IIS
> * Testování CI/CD kanálu

Přechod na další kurzu se dozvíte, jak nainstalovat SQL&#92;IIS&#92;.NET zásobníku na dvojice virtuálních počítačů Windows.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;.NET zásobníku](tutorial-iis-sql.md)