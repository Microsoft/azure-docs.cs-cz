---
title: Kurz – vytvoření kanálu CI/CD v Azure pomocí služby Team Services | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak vytvořit kanál pro průběžnou integraci a doručování, které se nasadí webová aplikace služby IIS na virtuálním počítači s Windows v Azure Visual Studio Team Services.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b23cec90573c4be73a73daf0bc0e793da012585c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37932088"
---
# <a name="tutorial-create-a-continuous-integration-pipeline-with-visual-studio-team-services-and-iis"></a>Kurz: Vytvoření kanálu průběžné integrace s Visual Studio Team Services a služby IIS
K automatizaci sestavování, testování a nasazení fáze vývoje aplikací, můžete použít průběžné integrace a nasazování (CI/CD) kanálu. V tomto kurzu vytvoříte kanál CI/CD pomocí Visual Studio Team Services a virtuální počítač (VM) Windows v Azure, na kterém běží služby IIS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Publikovat webovou aplikaci ASP.NET do projektu služby Team Services
> * Vytvořte definici sestavení, která se aktivuje při potvrzení změn kódu
> * Instalace a konfigurace služby IIS na virtuálním počítači v Azure
> * Instance služby IIS přidat do skupiny nasazení ve službě Team Services
> * Vytvoření verze definice publikování nového webu nasadit balíčky do služby IIS
> * Test kanálu CI/CD

Tento kurz vyžaduje modul Azure PowerShell verze 5.7.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="create-project-in-team-services"></a>Vytvoření projektu v Team Services
Visual Studio Team Services umožňuje snadnou spolupráci a vývoj bez zachování vytváří řešení pro správu místních kódu. Team Services nabízí cloudové testování kódu, sestavení a application insights. Můžete zvolit úložiště správy verzí a integrované vývojové prostředí, která nejlíp odpovídá vývoje kódu. Pro účely tohoto kurzu můžete si bezplatný účet pro vytvoření základní webové aplikace ASP.NET a kanál CI/CD. Pokud ještě nemáte účet služby Team Services [vytvořit](http://go.microsoft.com/fwlink/?LinkId=307137).

Správa procesu potvrzení kódu, definice sestavení a definice verzí, vytvoření projektu v Team Services následujícím způsobem:

1. Ve webovém prohlížeči otevřete řídicí panel služby Team Services a zvolte **nový projekt**.
2. Zadejte *myWebApp* pro **název projektu**. Ponechte všechna ostatní výchozí hodnoty pro použití *Git* správy verzí a *Agile* zpracování pracovní položky.
3. Zvolte možnost **nasdílet** *členové týmu*a pak vyberte **vytvořit**.
5. Po vytvoření projektu, zvolte možnost **inicializace s README nebo gitignore**, pak **inicializovat**.
6. Uvnitř nový projekt, zvolte **řídicí panely** v horní části, vyberte **otevřít v sadě Visual Studio**.


## <a name="create-aspnet-web-application"></a>Vytvořte webovou aplikaci ASP.NET
V předchozím kroku jste vytvořili projekt ve službě Team Services. V posledním kroku otevře nový projekt v sadě Visual Studio. Spravovat vaše potvrzení změn kódu v **Team Exploreru** okna. Vytvořit místní kopii nového projektu a pak vytvořte webovou aplikaci ASP.NET ze šablony následujícím způsobem:

1. Vyberte **klonování** k vytvoření místního úložiště git vašeho projektu služby Team Services.
    
    ![Naklonujte úložiště z projektu služby Team Services](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. V části **řešení**vyberte **nový**.

    ![Vytvoření řešení webové aplikace](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Vyberte **webové** šablony a klikněte na tlačítko **webová aplikace ASP.NET** šablony.
    1. Zadejte název pro vaši aplikaci, jako například *myWebApp*a zrušte zaškrtnutí políčka pro **vytvořit adresář pro řešení**.
    2. Pokud je k dispozici možnost, zrušte zaškrtnutí políčka pro **přidat službu Application Insights do projektu**. Application Insights je potřeba povolit webové aplikace pomocí Azure Application Insights. Pro zjednodušení ho v tomto kurzu, přeskočte tento proces.
    3. Vyberte **OK**.
4. Zvolte **MVC** ze seznamu šablon.
    1. Vyberte **změna ověřování**, zvolte **bez ověřování**a pak vyberte **OK**.
    2. Vyberte **OK** vytvořte řešení.
5. V **Team Exploreru** okně zvolte **změny**.

    ![Potvrdit místní změny do úložiště git Team Services](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. Do textového pole pro potvrzení, zadejte zprávu *prvotní potvrzení*. Zvolte **všechna potvrzení a synchronizace** z rozevírací nabídky.


## <a name="create-build-definition"></a>Vytvořte definici sestavení
Ve službě Team Services použít definici sestavení popisují, jak by měly být sestaveny vaší aplikace. V tomto kurzu vytvoříme základní definici, která přebírá našeho zdrojového kódu, sestaví řešení a pak vytvoří webové nasazení balíčku, které můžeme použít ke spuštění webové aplikace na serveru služby IIS.

1. V projektu služby Team Services, zvolte **sestavení a vydání** v horní části, vyberte **sestavení**.
3. Vyberte **+ nová definice**.
4. Zvolte **technologie ASP.NET (verze PREVIEW)** šablony a vyberte **použít**.
5. Ponechte všechny výchozí hodnoty úkolu. V části **získat zdroje**, ujistěte se, že *myWebApp* úložiště a *hlavní* jsou vybrané větve.

    ![Vytvořte definici sestavení v projektu služby Team Services](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. Na **triggery** kartu, nastavte posuvník pro **povolit tento trigger** k *povoleno*.
7. Uložit definici sestavení a fronty nového sestavení tak, že vyberete **Uložit & frontu** , pak **Uložit & frontu** znovu. Ponechte výchozí hodnoty a vyberte **fronty**.

Sledování podle sestavení je naplánováno na hostovaný agent potom začne sestavení. Výstup se podobá následujícímu příkladu:

![Úspěšné sestavení projektu služby Team Services](media/tutorial-vsts-iis-cicd/successful_build.png)


## <a name="create-virtual-machine"></a>Vytvoření virtuálního počítače
Poskytovat platformu pro spuštění vaší webové aplikace ASP.NET, je třeba virtuální počítač Windows, na kterém běží služby IIS. Team Services používá k interakci s instancí služby IIS jako potvrzení změn kódu a sestavení se aktivují agenta.

Vytvoření virtuálního počítače Windows serveru 2016 s [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). Následující příklad vytvoří virtuální počítač s názvem *myVM* v *USA – východ* umístění. Skupina prostředků *myResourceGroupVSTS* a vytvoří se také podpůrné síťové prostředky. Pro povolení webového provozu, TCP port *80* otevření k virtuálnímu počítači. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které má být použit jako přihlašovací údaje pro virtuální počítač:

```powershell
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine
New-AzureRmVM `
  -ResourceGroupName "myResourceGroupVSTS" `
  -Name "myVM" `
  -Location "East US" `
  -ImageName "Win2016Datacenter" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -SecurityGroupName "myNetworkSecurityGroup" `
  -PublicIpAddressName "myPublicIp" `
  -Credential $cred `
  -OpenPorts 80
```

Pro připojení k vašemu virtuálnímu počítači, získejte veřejnou IP adresu pomocí [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) následujícím způsobem:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" | Select IpAddress
```

Vytvořte relaci vzdálené plochy k virtuálnímu počítači:

```cmd
mstsc /v:<publicIpAddress>
```

Na virtuálním počítači otevřete **Powershellu správce** příkazového řádku. Instalace IIS a požadované součásti .NET následujícím způsobem:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## <a name="create-deployment-group"></a>Vytvoření skupiny nasazení
Vydat webu nasadit balíček na server služby IIS, můžete definovat skupiny nasazení ve službě Team Services. Tuto skupinu můžete zadat, serverů, které jsou cílem nové buildy potvrzení změn kódu do služby Team Services a po dokončení sestavení.

1. Ve službě Team Services, zvolte **sestavení a vydání** a pak vyberte **skupiny nasazení**.
2. Zvolte **skupiny přidat nasazení**.
3. Zadejte název skupiny, jako například *mojeiis*a pak vyberte **vytvořit**.
4. V **registraci počítačů** části, ujistěte se, *Windows* je vybrána a potom zaškrtněte políčko **pomocí osobního přístupového tokenu ve skriptu pro ověřování**.
5. Vyberte **zkopírujte skript do schránky**.


### <a name="add-iis-vm-to-the-deployment-group"></a>Virtuální počítač služby IIS přidat do skupiny nasazení
S vytvořenou skupinu nasazení přidejte do skupiny každá instance služby IIS. Vygeneruje skript, který se stáhne a nakonfiguruje agenta na virtuálním počítači, který obdrží nové webové služby Team Services nasazení balíčků a platí pro službu IIS.

1. Zpátky **Powershellu správce** vložte relaci na vašem virtuálním počítači a spusťte skript zkopírovali ze služby Team Services.
2. Po zobrazení výzvy ke konfiguraci značky pro agent, zvolte *Y* a zadejte *webové*.
3. Po zobrazení výzvy pro uživatelský účet, stiskněte klávesu *vrátit* přijměte výchozí hodnoty.
4. Čekat na dokončení a zobrazí se zpráva skriptu *vstsagent.account.computername služby se úspěšně spustila*.
5. V **skupiny nasazení** stránku **sestavení a vydání** nabídky, otevřete *mojeiis* skupiny nasazení. Na **počítače** kartu, ověřte, že je uvedený váš virtuální počítač.

    ![Virtuální počítač se úspěšně přidal do skupiny nasazení služby Team Services](media/tutorial-vsts-iis-cicd/deployment_group.png)


## <a name="create-release-definition"></a>Vytvořte definici vydané verze
Pokud chcete publikovat svá sestavení, vytvoření definice verze v Team Services. Tato definice se automaticky aktivuje úspěšný build vaší aplikace. Zvolte skupinu nasazení tak, aby nabízel webu nasadit balíček a definovat odpovídající nastavení služby IIS.

1. Zvolte **sestavení a vydání**a pak vyberte **sestavení**. Vyberte definici sestavení vytvořené v předchozím kroku.
2. V části **nedávno dokončenou**, zvolte nejnovější sestavení a pak vyberte **vydání**.
3. Zvolte **Ano** na vytvoření definice verze.
4. Zvolte **prázdný** šablony, pak vyberte **Další**.
5. Ověřte definici sestavení projektu a zdroje jsou vyplněna váš projekt.
6. Vyberte **průběžné nasazování** zaškrtněte políčko a potom vyberte **vytvořit**.
7. Vyberte rozevírací seznam vedle **+ přidat úkoly** a zvolte *přidat fázi skupiny nasazení*.
    
    ![Přidat úlohu ve službě Team Services definice vydané verze](media/tutorial-vsts-iis-cicd/add_release_task.png)

8. Zvolte **přidat** vedle **Deploy(Preview) aplikace webové služby IIS**a pak vyberte **Zavřít**.
9. Vyberte **spustit ve skupině nasazení** nadřazené úloze.
    1. Pro **skupina nasazení**, vyberte skupinu nasazení, kterou jste vytvořili dříve, jako například *mojeiis*.
    2. V **Machine značky** vyberte **přidat** a zvolte *webové* značky.
    
    ![Verze úlohy nasazení skupiny definic pro službu IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Vyberte **nasazení: nasazení aplikace Web služby IIS** úloh mohli konfigurovat svá nastavení instance služby IIS následujícím způsobem:
    1. Pro **název webu**, zadejte *výchozí webový server*.
    2. Ponechte všechna ostatní výchozí nastavení.
12. Zvolte **Uložit**a pak vyberte **OK** dvakrát.


## <a name="create-a-release-and-publish"></a>Vytvoření vydané verze a publikování
Teď může nasdílet změny webu nasadit jako novou verzi balíčku. Tento krok komunikuje s agentem na každou instanci, která je součástí skupiny nasazení, nabízených oznámení na webu nasadit balíček a pak nakonfiguruje službu IIS ke spuštění aktualizované webové aplikace.

1. Do definice vydané verze, vyberte **+ vydání**, klikněte na tlačítko **vytvořit vydání**.
2. Ověřte, zda nejnovější sestavení je vybrána v rozevíracím seznamu, spolu s **automatizované nasazení: Po vytvoření vydání**. Vyberte **Vytvořit**.
3. V horní části definice vydané verze, jako například zobrazí banner s malé *vydání "Release-1" vytvořil*. Vyberte odkaz pro vydání.
4. Otevřít **protokoly** kartě sledovat průběh vydání.
    
    ![Úspěšné vydání verze Team Services a webové nasazení balíčku push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. Po dokončení vydání, otevřete webový prohlížeč a zadejte veřejné Zkoumání adresu svého virtuálního počítače. Webové aplikace v ASP.NET běží.

    ![Webová aplikace ASP.NET poběží na virtuálním počítači služby IIS](media/tutorial-vsts-iis-cicd/running_web_app.png)


## <a name="test-the-whole-cicd-pipeline"></a>Otestovat celý kanál CI/CD
S vaší webovou aplikací ve službě IIS pokuste se nyní celý kanál CI/CD. Když provedete změny v sadě Visual Studio a potvrzení, na které se aktivuje kódu, sestavení, které potom aktivuje verzi aktualizované webové nasaďte balíček do služby IIS:

1. V sadě Visual Studio, otevřete **Průzkumníka řešení** okna.
2. Přejděte na a otevřete *myWebApp | Zobrazení | Domů | Index.cshtml*
3. Upravte řádek 6 pro čtení:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Uložte soubor.
5. Otevřít **Team Exploreru** okna, vyberte *myWebApp* projektu a pak zvolte **změny**.
6. Zadejte zprávu potvrzení změn, jako například *kanálu CI/CD testování*, klikněte na tlačítko **Potvrdit vše a synchronizovat** z rozevírací nabídky.
7. V pracovním prostoru Team Services se spustí nové sestavení od potvrzení změn kódu. 
    - Zvolte **sestavení a vydání**a pak vyberte **sestavení**. 
    - Vyberte definici sestavení a pak vyberte **zařazeno do fronty a spuštění** sestavení ke sledování průběhu sestavení.
9. Po úspěšném sestavení se aktivuje novou verzi.
    - Zvolte **sestavení a vydání**, pak **verze** zobrazíte na webu nasadit balíček nahrány do vašeho virtuálního počítače služby IIS. 
    - Vyberte **aktualizovat** ikony pro aktualizaci stavu. Když *prostředí* sloupci se zobrazuje zelená značka zaškrtnutí, vydání úspěšně nasadil do služby IIS.
11. Pokud chcete zobrazit vaše změny se použily, aktualizujte váš web služby IIS v prohlížeči.

    ![Webová aplikace ASP.NET poběží na virtuálním počítači služby IIS z kanálu CI/CD](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu vytvoříte webovou aplikaci ASP.NET ve službě Team Services a nakonfigurujete sestavení a definice vydání k nasazení nové webové nasazení balíčků do služby IIS na každém potvrzení kódu. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Publikovat webovou aplikaci ASP.NET do projektu služby Team Services
> * Vytvořte definici sestavení, která se aktivuje při potvrzení změn kódu
> * Instalace a konfigurace služby IIS na virtuálním počítači v Azure
> * Instance služby IIS přidat do skupiny nasazení ve službě Team Services
> * Vytvoření verze definice publikování nového webu nasadit balíčky do služby IIS
> * Test kanálu CI/CD

Pokračujte k dalšímu kurzu, kde se naučíte, jak nainstalovat SQL&#92;IIS&#92;zásobník .NET na pár virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [SQL&#92;IIS&#92;zásobník .NET](tutorial-iis-sql.md)