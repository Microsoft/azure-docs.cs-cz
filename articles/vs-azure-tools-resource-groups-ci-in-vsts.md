---
title: Průběžnou integraci ve Visual Studio Team Services pomocí projekty skupiny prostředků Azure | Microsoft Docs
description: Popisuje, jak nastavit průběžnou integraci ve Visual Studio Team Services pomocí projekty nasazení skupiny prostředků Azure v sadě Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: fc5a45c899cd72c051dd08f7db039565a57381a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192940"
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Průběžnou integraci ve Visual Studio Team Services pomocí projekty nasazení skupiny prostředků Azure
Pokud chcete nasadit šablonu Azure, můžete provádět úlohy v různých fázích: sestavení, testovací, kopírovat do Azure (také nazývané "Přípravy") a nasadit šablonu. Existují dva různé způsoby pro nasazení šablon pro Visual Studio Team Services (Visual Studio Team Services). Obě metody zadejte stejné výsledky, takže vybrat tu, které nejlépe vyhovuje pracovního postupu.

1. Přidejte jeden krok do vaší definice sestavení, který spouští skript prostředí PowerShell, který je zahrnutý v projektu nasazení skupiny prostředků Azure (nasadit-AzureResourceGroup.ps1). Skript zkopíruje artefaktů a poté nasadí šablony.
2. Přidáte že více Visual Studio Team Services kroky sestavení, každé z nich provedení úlohy fáze.

Tento článek ukazuje obě možnosti. První možnost nabízí výhodu v podobě pomocí stejného skriptu používané vývojáři v sadě Visual Studio a poskytnete konzistence v průběhu cyklu. Druhou možností je umístěna do předdefinovaných skriptu. Obě postupech se předpokládá, že již máte nasazení projektu sady Visual Studio zaškrtnutí do Visual Studio Team Services.

## <a name="copy-artifacts-to-azure"></a>Zkopírování artefaktů na Azure
Bez ohledu na to scénář Pokud máte artefakty, které jsou potřebné pro nasazení šablony, je nutné udělit Azure Resource Manager přístup k nim. Tyto artefakty mohou zahrnovat například soubory:

* Vnořené šablony
* Konfiguračních skriptů a skripty, DSC
* Binární soubory aplikace

### <a name="nested-templates-and-configuration-scripts"></a>Vnořené šablony a konfigurační skripty
Při použití šablon, které aplikace Visual Studio (nebo vytvořené s nástroji Visual Studio fragmenty), skript prostředí PowerShell nejen zpracuje artefakty, je také parameterizes identifikátor URI pro prostředky pro jiné nasazení. Skript pak zkopíruje artefakty do zabezpečeného kontejneru ve službě Azure, vytvoří SaS token pro tento kontejner a pak předá tyto informace k nasazení šablony. V tématu [vytvořit šablonu nasazení](https://msdn.microsoft.com/library/azure/dn790564.aspx) Další informace o vnořené šablony.  Při použití úlohy v Visual Studio Team Services, musíte vybrat odpovídající úlohy pro šablonu nasazení a v případě potřeby předat hodnoty parametrů z pracovní kroku nasazení šablony.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Nastavit průběžné nasazování ve Visual Studio Team Services
Volat skript prostředí PowerShell v Visual Studio Team Services, musíte aktualizovat svou definici sestavení. Stručně řečeno jsou kroky: 

1. Upravte definici sestavení.
2. Nastavení Azure autorizace ve Visual Studio Team Services.
3. Přidejte krok sestavení Azure PowerShell, který odkazuje na skript prostředí PowerShell v projektu nasazení skupiny prostředků Azure.
4. Nastavte hodnotu *- ArtifactsStagingDirectory* parametr pro práci s projektem součástí Visual Studio Team Services.

### <a name="detailed-walkthrough-for-option-1"></a>Podrobný návod pro možnost 1
Následující postup vás provede kroky nutné ke konfiguraci průběžné nasazování v Visual Studio Team Services pomocí jednu úlohu, která se spouští skript prostředí PowerShell ve vašem projektu. 

1. Upravit definici sestavení vaší Visual Studio Team Services a přidejte krok sestavení prostředí Azure PowerShell. Zvolte definici sestavení v části **sestavení definice** kategorie a potom zvolte **upravit** odkaz.
   
   ![Upravit definici sestavení][0]
2. Přidejte nový **prostředí Azure PowerShell** sestavení krok k definici sestavení a potom zvolte **krok sestavení přidat...** .
   
   ![Přidejte krok sestavení][1]
3. Vyberte **úloh nasadit** kategorie, vyberte **prostředí Azure PowerShell** úkolů a potom vyberte jeho **přidat** tlačítko.
   
   ![Přidání úkolů][2]
4. Vyberte **prostředí Azure PowerShell** kroku sestavení a pak zadejte jeho hodnoty.
   
   1. Pokud je již přidána do Visual Studio Team Services koncový bod služby Azure, zvolte předplatné v **předplatné Azure** rozevíracího seznamu a pak skip k další části. 
      
      Pokud nemáte koncový bod služby Azure ve Visual Studio Team Services, budete muset přidat. Tato část vás provede procesem. Pokud váš účet Azure používá účet Microsoft (například Hotmail), musíte provést následující kroky k získání objektu služby ověřování.
   2. Vyberte **spravovat** na odkaz vedle položky **předplatné Azure** rozevíracího seznamu.
      
      ![Spravovat předplatná Azure][3]
   3. Zvolte **Azure** v **nový koncový bod služby** rozevíracího seznamu.
      
      ![Nový koncový bod služby][4]
   4. V **přidat předplatné Azure** dialogové okno, vyberte **instanční objekt** možnost.
      
      ![Hlavní možnosti služby][5]
   5. Přidat vaše údaje předplatného Azure **přidat předplatné Azure** dialogové okno. Je třeba zadat následující položky:
      
      * ID předplatného
      * Název předplatného
      * Id objektu zabezpečení
      * Klíč objektu služby
      * ID tenanta
   6. Přidat název vaší volby **předplatné** pole název. Tato hodnota se zobrazí později v **předplatné Azure** rozevíracího seznamu ve Visual Studio Team Services. 
   7. Pokud si nejste jisti svoje ID předplatného Azure, můžete jeden z následujících příkazů ho Pokud chcete zjistit.
      
      Pro skripty prostředí PowerShell použijte:
      
      `Get-AzureRmSubscription`
      
      Pokud používáte Azure CLI, použijte:
      
      `azure account show`
   8. Získání ID objektu služby, klíč objektu služby a ID klienta, použijte postup v [vytvoření aplikace Active Directory a objektu zabezpečení pomocí portálu](resource-group-create-service-principal-portal.md) nebo [ověřování hlavní název služby pomocí Azure Resource Manageru](resource-group-authenticate-service-principal.md).
   9. Přidejte hodnoty pro ID objektu služby, klíč objektu služby a ID klienta **přidat předplatné Azure** dialogové okno pole a zvolte **OK** tlačítko.
      
      Nyní máte platný hlavní název služby se použije ke spuštění skriptu prostředí Azure PowerShell.
5. Upravit definici sestavení a zvolte **prostředí Azure PowerShell** kroku sestavení. Vyberte předplatné v **předplatné Azure** rozevíracího seznamu. (Pokud se nezobrazí předplatného, zvolte **aktualizovat** tlačítko Další **spravovat** odkaz.) 
   
   ![Konfigurace prostředí Azure PowerShell úlohu sestavení][8]
6. Zadejte cestu ke skriptu prostředí PowerShell AzureResourceGroup.ps1 nasadit. To pokud chcete udělat, zvolte tlačítko se třemi tečkami (...) vedle položky **cestu ke skriptu** pole, přejděte do skriptu prostředí PowerShell AzureResourceGroup.ps1 nasadit **skripty** složku projekt, vyberte ji a potom vyberte **OK** tlačítko.    
   
   ![Vyberte cestu ke skriptu][9]
7. Po výběru skript aktualizovat cestu skript tak, aby je spuštěn z Build.StagingDirectory (stejný adresář, *ArtifactsLocation* je nastaven na). To provedete tak, že přidáte "$(Build.StagingDirectory)/"na začátku cesta ke skriptu.
   
    ![Upravit cestu ke skriptu][10]
8. V **argumenty skriptu** zadejte následující parametry (v jednom řádku). Pokud spustíte skript v sadě Visual Studio, můžete zobrazit používání parametrů v VS **výstup** okno. Můžete to použít jako výchozí bod pro nastavení hodnoty parametru v kroku vaše sestavení.
   
   | Parametr | Popis |
   | --- | --- |
   | -ResourceGroupLocation |Hodnota geografického umístění, kde je umístěna, například skupina prostředků **eastus** nebo **, východní USA,**. (Pokud je mezera v názvu, přidejte jednoduchých uvozovkách.) V tématu [oblasti Azure](https://azure.microsoft.com/regions/) Další informace. |
   | -ResourceGroupName |Název skupiny prostředků použít pro toto nasazení. |
   | -UploadArtifacts |Tento parametr, pokud jsou k dispozici, určuje, že artefaktů, které musí být nahrán do Azure z místního systému. Stačí nastavit tento přepínač. Pokud vaše šablony nasazení vyžaduje další artefakty, pomocí kterých chcete dvoufázové instalace pomocí skriptu prostředí PowerShell (například konfigurační skripty nebo vnořené šablony). |
   | -StorageAccountName |Název účtu úložiště používat k artefaktům fáze pro toto nasazení. Tento parametr se používá pouze pokud jsou pracovní artefaktů pro nasazení. Pokud je tento parametr zadaný, se vytvoří nový účet úložiště, pokud skript není vytvořili během předchozí nasazení. Pokud je zadán parametr, již musí existovat účet úložiště. |
   | -StorageAccountResourceGroupName |Název skupiny prostředků, které jsou přidružené k účtu úložiště. Tento parametr je povinný, jenom v případě, že je zadat hodnotu pro parametr StorageAccountName. |
   | -TemplateFile |Cesta k souboru šablony v projektu nasazení skupiny prostředků Azure. Ke zvýšení flexibility, použijte cestu pro tento parametr, který je relativní k umístění skriptu prostředí PowerShell místo absolutní cesta. |
   | -TemplateParametersFile |Cesta k souboru parametrů v projektu nasazení skupiny prostředků Azure. Ke zvýšení flexibility, použijte cestu pro tento parametr, který je relativní k umístění skriptu prostředí PowerShell místo absolutní cesta. |
   | -ArtifactStagingDirectory |Tento parametr umožňuje PowerShell skriptu vědět složce, ze kterých by se měl zkopírovat binární soubory projektu. Tato hodnota přepíše výchozí hodnota používaná pro skript prostředí PowerShell. Pro Visual Studio Team Services používat, nastavte hodnotu na: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Tady je příklad skriptu argumenty (řádku přerušený čitelnější):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Jakmile budete hotovi, **argumenty skriptu** pole by měl vypadat podobně jako v následujícím seznamu:
   
   ![Argumenty skriptu][11]
9. Po přidání všechny požadované položky k sestavení kroku prostředí Azure PowerShell, vyberte **fronty** sestavení tlačítko a tím projekt sestavit. **Sestavení** obrazovka ukazuje výstup ze skriptu prostředí PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Podrobný návod pro možnost 2
Následující postup vás provede kroky nutné ke konfiguraci průběžné nasazování v Visual Studio Team Services pomocí předdefinované úlohy.

1. Upravte svou definici sestavení Visual Studio Team Services. Chcete-li přidat že kroky dva nové sestavení. Zvolte definici sestavení v části **sestavení definice** kategorie a potom zvolte **upravit** odkaz.
   
   ![Upravit definice sestavení][12]
2. Přidat nové sestavení kroky pro definici sestavení s využitím **krok sestavení přidat...** .
   
   ![Přidejte krok sestavení][13]
3. Vyberte **nasadit** kategorii úloh, vyberte **Azure File Copy** úkolů a potom vyberte jeho **přidat** tlačítko.
   
   ![Přidat úloha Azure File Copy][14]
4. Vyberte **nasazení skupiny prostředků Azure** úkolů, a potom vyberte jeho **přidat** tlačítko a potom **Zavřít** **úloha katalogu**.
   
   ![Přidat úloha nasazení skupiny prostředků Azure][15]
5. Vyberte **Azure File Copy** úloh a zadejte jeho hodnoty.
   
   Pokud je již přidána do Visual Studio Team Services koncový bod služby Azure, zvolte předplatné v **předplatné Azure** rozevíracího seznamu. Pokud nemáte předplatné, přečtěte si téma [možnost 1](#detailed-walkthrough-for-option-1) pokyny k nastavení jeden ve Visual Studio Team Services.
   
   * Zdroj – zadejte **$(Build.StagingDirectory)**
   * Azure typu připojení – vyberte **Azure Resource Manager**
   * Předplatné Azure RM - vyberte předplatné, pro kterou chcete použít v účtu úložiště **předplatné Azure** rozevíracího seznamu. Pokud předplatné se nezobrazí, vyberte **aktualizovat** tlačítko Další **spravovat** odkaz.
   * Cílový typ - vyberte **objektů Blob v Azure**
   * RM účet úložiště – vyberte účet úložiště byste chtěli použít pro přípravu artefaktů
   * Název kontejneru – zadejte název kontejneru, který chcete použít pro pracovní; může být jakýkoli název kontejneru platný, ale používat jeden vyhrazený pro tuto definici sestavení
   
   Pro výstup hodnoty:
   
   * Zadejte kontejner úložiště URI - **artifactsLocation**
   * Token SAS pro kontejner úložiště – zadejte **artifactsLocationSasToken**
   
   ![Konfigurace úloze Azure File Copy][16]
6. Vyberte **nasazení skupiny prostředků Azure** kroku sestavení a pak zadejte jeho hodnoty.
   
   * Azure typu připojení – vyberte **Azure Resource Manager**
   * Předplatné Azure RM - vyberte odběr služby pro nasazení v **předplatné Azure** rozevíracího seznamu. Obvykle to bude stejné předplatné použité v předchozím kroku
   * Akce – vyberte **vytvořit nebo aktualizovat skupinu prostředků**
   * Skupiny prostředků – vyberte skupinu prostředků nebo zadejte název nové skupiny prostředků pro nasazení
   * Umístění – vyberte umístění pro skupinu prostředků
   * Šablona – zadejte cestu a název šablony, kterou chcete nasadit předřazení **$(Build.StagingDirectory)**, například: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Parametry šablony – zadejte cestu a název parametry, které chcete použít, předřazení **$(Build.StagingDirectory)**, například: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Přepsání parametry šablony – zadejte nebo zkopírujte a vložte následující kód:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Konfigurace úlohy nasazení skupiny prostředků Azure][17]
7. Po přidání všechny požadované položky, Uložit definici sestavení a zvolte **fronty nového sestavení** v horní části.

## <a name="next-steps"></a>Další postup
Čtení [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md) Další informace o Azure Resource Manageru a skupin prostředků Azure.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
