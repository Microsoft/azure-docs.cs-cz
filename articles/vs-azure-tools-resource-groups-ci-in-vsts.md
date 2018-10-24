---
title: Průběžná integrace služby Azure DevOps pomocí projekty skupiny prostředků Azure | Dokumentace Microsoftu
description: Popisuje, jak nastavit průběžnou integraci služby Azure DevOps s využitím projekty nasazení skupiny prostředků Azure v sadě Visual Studio.
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
ms.openlocfilehash: a3dfca48b52af39e7a536b3012a3f4cdac4e9a94
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955163"
---
# <a name="continuous-integration-in-azure-devops-services-using-azure-resource-group-deployment-projects"></a>Průběžná integrace služby Azure DevOps pomocí projekty nasazení skupiny prostředků Azure
K nasazení šablony Azure, můžete provádět úlohy v různých fázích: sestavení, testování, kopírovat do Azure (tzv. "Přípravného") a nasaďte šablonu. Existují dva různé způsoby nasazování šablon ke službám Azure DevOps. Obě metody poskytují stejné výsledky, proto zvolte ten, který nejlépe vyhovuje požadavkům vašeho pracovního postupu.

1. Přidáte jednoho kroku do vašeho kanálu sestavení, který spustí skript prostředí PowerShell, který je součástí nasazení projektu skupiny prostředků Azure (Deploy-AzureResourceGroup.ps1). Tento skript zkopíruje artefakty a pak nasadí šablony.
2. Přidání že více služeb Azure DevOps kroky sestavení, každý z nich provedení úlohy fázi.

Tento článek ukazuje obě možnosti. První možnost nabízí výhodu v podobě pomocí stejného skriptu vývojářům v sadě Visual Studio a poskytnutí konzistence v průběhu životního cyklu. Druhou možností je vhodnou alternativou integrované skriptu. Oba postupy předpokládají, že již máte Visual Studio projekt nasazení zapsány do služby Azure DevOps.

## <a name="copy-artifacts-to-azure"></a>Zkopírování artefaktů do Azure
Bez ohledu na scénář Pokud mají všechny artefakty, které jsou potřebné pro nasazení šablony, musíte poskytnout přístup k Azure Resource Manageru k nim. Tyto artefakty mohou zahrnovat například soubory:

* Vnořené šablony
* Konfigurace a skriptů DSC
* Binární soubory aplikace

### <a name="nested-templates-and-configuration-scripts"></a>Vnořené šablony a skripty konfigurace
Při použití šablony, které poskytuje Visual Studio (nebo sestavené pomocí sady Visual Studio fragmenty), skript prostředí PowerShell fáze nejen artefakty, také parametrizuje identifikátor URI pro prostředky pro jiné nasazení. Skript potom zkopíruje artefakty do zabezpečeného kontejneru v Azure, vytvoří SaS token pro tento kontejner a pak předá tyto informace k nasazení šablony. Zobrazit [vytvoření šablony nasazení](https://msdn.microsoft.com/library/azure/dn790564.aspx) Další informace o vnořených šablonách.  Při použití úkolů ve službě Azure DevOps Services, musíte vybrat příslušné úlohy pro nasazení šablony a v případě potřeby předat hodnoty parametrů z pracovní kroku k nasazení šablony.

## <a name="set-up-continuous-deployment-in-azure-pipelines"></a>Nastavení průběžného nasazování v kanálech Azure
Volání skriptu prostředí PowerShell v kanálech Azure, budete muset aktualizovat vašeho kanálu sestavení. Stručně řečeno postup je: 

1. Upravte kanál sestavení.
2. Nastavení Azure autorizace v kanálech Azure.
3. Přidejte krok sestavení prostředí Azure PowerShell, který odkazuje na skript prostředí PowerShell v projektu nasazení skupiny prostředků Azure.
4. Nastavte hodnotu *- ArtifactsStagingDirectory* parametr pro práci s projektem vytvořené v kanálech Azure.

### <a name="detailed-walkthrough-for-option-1"></a>Podrobný návod pro možnost 1
Následující postup vás provede kroky potřebnými ke konfiguraci průběžného nasazování služby Azure DevOps pomocí jednu úlohu, která spouští skript prostředí PowerShell ve vašem projektu. 

1. Upravte svůj kanál služby Azure DevOps sestavení a přidat krok sestavení Azure Powershellu. Vyberte kanál sestavení v rámci **vytvářet kanály** kategorie a klikněte na tlačítko **upravit** odkaz.
   
   ![Upravit sestavení kanálu][0]
2. Přidat nový **prostředí Azure PowerShell** krok sestavení kanálu sestavení a klikněte na tlačítko **přidat krok sestavení...** .
   
   ![Přidejte krok sestavení][1]
3. Zvolte **úkol nasadit** kategorii, vyberte **prostředí Azure PowerShell** úloh a klikněte na tlačítko jeho **přidat** tlačítko.
   
   ![Přidat úlohy][2]
4. Zvolte **prostředí Azure PowerShell** krok sestavení a potom vyplňte hodnoty.
   
   1. Pokud už máte koncový bod služby Azure do služby Azure DevOps, vyberte předplatné, v **předplatné Azure** rozevíracího seznamu a potom přejít k další části. 
      
      Pokud nemáte koncový bod služby Azure ve službě Azure DevOps Services, musíte ho přidat. Tato část vás provede procesem. Používá-li váš účet Azure účtem Microsoft (Hotmail), je nutné provést následující kroky k získání ověřování instančního objektu.

   2. Zvolte **spravovat** odkaz **předplatné Azure** rozevíracího seznamu.
      
      ![Správa předplatných Azure][3]
   3. Zvolte **Azure** v **nový koncový bod služby** rozevíracího seznamu.
      
      ![Nový koncový bod služby][4]
   4. V **přidat předplatné Azure** dialogové okno, vyberte **instanční objekt služby** možnost.
      
      ![Možnosti instančního objektu služby][5]
   5. Přidejte své informace o předplatném Azure k **přidat předplatné Azure** dialogové okno. Budete muset zadat následující položky:
      
      * ID předplatného
      * Název předplatného
      * Id instančního objektu
      * Klíč objektu služby
      * ID tenanta
   6. Přidání názvu vlastní volby **předplatné** pole název. Tato hodnota se zobrazí později v **předplatné Azure** rozevíracího seznamu ve službě Azure DevOps Services. 

   7. Pokud si nejste jisti ID vašeho předplatného Azure, můžete použít jednu z následujících příkazů ho načíst.
      
      U skriptů prostředí PowerShell použijte:
      
      `Get-AzureRmSubscription`
      
      Pokud používáte Azure CLI, použijte:
      
      `azure account show`
   8. Chcete-li získat ID instančního objektu služby, klíč instančního objektu a ID Tenanta, postupujte podle postupu [vytvoření aplikace Active Directory a instančního objektu pomocí portálu](active-directory/develop/howto-create-service-principal-portal.md) nebo [ověřování instančního objektu pomocí Azure Resource Manager](active-directory/develop/howto-authenticate-service-principal-powershell.md).
   9. Přidejte ID instančního objektu služby, klíč instančního objektu a ID Tenanta hodnoty, které mají **přidat předplatné Azure** dialogové okno a potom vyberte **OK** tlačítko.
      
      Teď máte platný hlavní název služby se použije ke spuštění skriptu Azure Powershellu.
5. Upravte kanál sestavení a zvolte **prostředí Azure PowerShell** krok sestavení. Vyberte předplatné, ve **předplatné Azure** rozevíracího seznamu. (Pokud není zobrazená předplatném, zvolte **aktualizovat** tlačítko vedle **spravovat** propojení.) 
   
   ![Konfigurace prostředí Azure PowerShell úloha sestavení][8]
6. Zadejte cestu k nasazení AzureResourceGroup.ps1 Powershellového skriptu. Chcete-li to provést, zvolte tlačítko se třemi tečkami (...) vedle položky **cesta ke skriptu** , přejděte na skript Powershellu nasadit AzureResourceGroup.ps1 v **skripty** složky vašeho projektu, vyberte ho a pak Zvolte **OK** tlačítko.    
   
   ![Vyberte cestu ke skriptu][9]
7. Po výběru skript aktualizaci cesty skript tak, aby je spouštět z Build.StagingDirectory (stejný adresář, který *ArtifactsLocation* je nastavena na). Můžete to provést tak, že přidáte "$(Build.StagingDirectory)/"na začátku cesta ke skriptu.
   
    ![Upravit cestu ke skriptu][10]
8. V **argumenty skriptu** zadejte následující parametry (v jednom řádku). Při spuštění skriptu v sadě Visual Studio se zobrazí, jak používá parametry ve VS **výstup** okna. Můžete to použít jako výchozí bod pro nastavení hodnot parametru v kroku vašeho sestavení.
   
   | Parametr | Popis |
   | --- | --- |
   | -ResourceGroupLocation |Hodnota geografického umístění, kde je umístěna, například skupina prostředků **eastus** nebo **: východní USA"**. (Jednoduché uvozovky přidejte, pokud název obsahuje mezery.) Zobrazit [oblastí Azure](https://azure.microsoft.com/regions/) Další informace. |
   | – Název skupiny prostředků |Název skupiny prostředků, použijete pro toto nasazení. |
   | -UploadArtifacts |Tento parametr, pokud je přítomen, určuje, že artefakty, které je třeba nahrát do Azure z místního systému. Stačí nastavit tento přepínač, pokud vaše nasazení šablony vyžaduje další artefakty, které chcete připravit pomocí skriptu prostředí PowerShell (například konfigurační skripty nebo vnořené šablony). |
   | -StorageAccountName |Název účtu úložiště pro artefakty fáze pro toto nasazení. Tento parametr se používá, pouze pokud představují přípravné artefakty pro nasazení. Pokud tento parametr zadán, je vytvořen nový účet úložiště, pokud skript není vytvořen během předchozích nasazení. Pokud je zadán parametr, již musí existovat účet úložiště. |
   | -StorageAccountResourceGroupName |Název skupiny prostředků, které jsou přidružené k účtu úložiště. Tento parametr je povinný, jenom v případě, že zadáte hodnotu pro parametr StorageAccountName. |
   | -TemplateFile |Cesta k souboru šablony v projektu nasazení skupiny prostředků Azure. Ke zvýšení flexibility, použijte cestu pro tento parametr, který je relativní k umístění skriptu Powershellu místo absolutní cestu. |
   | -TemplateParametersFile |Cesta k souboru parametrů v projektu nasazení skupiny prostředků Azure. Ke zvýšení flexibility, použijte cestu pro tento parametr, který je relativní k umístění skriptu Powershellu místo absolutní cestu. |
   | -ArtifactStagingDirectory |Tento parametr umožňuje PowerShell skriptu vědět složku z kde by zkopírovat binární soubory projektu. Tato hodnota přepíše výchozí hodnota používaná pro skript prostředí PowerShell. Pro služby Azure DevOps používají, nastavte hodnotu na: - ArtifactStagingDirectory $(Build.StagingDirectory) |
   
   Tady je příklad skriptu argumenty (řádek rozdělit pro lepší čitelnost):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Jakmile budete hotovi, **argumenty skriptu** pole by mělo vypadat jako v následujícím seznamu:
   
   ![Argumenty skriptu][11]
9. Po přidání všechny požadované položky do prostředí Azure PowerShell kroku sestavení, zvolte **fronty** vytvářet tlačítka pro sestavení projektu. **Sestavení** obrazovce se zobrazí výstup ze skriptu prostředí PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Podrobný návod pro možnost 2
Následující postup vás provede kroky potřebnými ke konfiguraci průběžného nasazování služby Azure DevOps pomocí připravených úloh.

1. Upravte svůj kanál služby Azure DevOps sestavení přidat že dvě nové kroky sestavení. Vyberte kanál sestavení v rámci **definice sestavení** kategorie a klikněte na tlačítko **upravit** odkaz.
   
   ![Upravit definici sestavení][12]
2. Přidat nové kroky sestavení k sestavení pomocí kanálu **přidat krok sestavení...** .
   
   ![Přidejte krok sestavení][13]
3. Zvolte **nasadit** kategorii úkolů, vyberte **Azure File Copy** úloh a klikněte na tlačítko jeho **přidat** tlačítko.
   
   ![Přidat úlohu Azure File Copy][14]
4. Zvolte **nasazení skupiny prostředků Azure** úloh a pak vyberte jeho **přidat** tlačítko a pak **Zavřít** **katalogu úloh**.
   
   ![Přidat úkol nasazení skupiny prostředků Azure][15]
5. Zvolte **Azure File Copy** úloh a zadejte jeho hodnoty.
   
   Pokud už máte koncový bod služby Azure do služby Azure DevOps, vyberte předplatné, v **předplatné Azure** rozevíracího seznamu. Pokud předplatné nemáte, přečtěte si téma [možnost 1](#detailed-walkthrough-for-option-1) pokyny k jejímu nastavení služby Azure DevOps.
   
   * Source – zadání **$(Build.StagingDirectory)**
   * Azure typ připojení – vyberte **Azure Resource Manageru**
   * Předplatné Azure RM - vyberte předplatné, pro kterou chcete použít v účtu úložiště **předplatné Azure** rozevíracího seznamu. Pokud předplatné není zobrazená, zvolte **aktualizovat** tlačítko Další **spravovat** odkaz.
   * Cílový typ - vyberte **objektů Blob v Azure**
   * Správce prostředků účet úložiště – vyberte účet úložiště, které chcete použít pro přípravu artefakty
   * Název kontejneru – zadejte název kontejneru, který chcete použít pro pracovní; To může být jakýkoli název platný kontejner, ale používat jeden vyhrazený pro tento kanál sestavení
   
   Pro výstupní hodnoty:
   
   * Zadejte kontejner úložiště URI - **artifactsLocation**
   * Token SAS kontejneru úložiště – zadejte **artifactsLocationSasToken**
   
   ![Konfigurace úloh Azure File Copy][16]
6. Zvolte **nasazení skupiny prostředků Azure** krok sestavení a potom vyplňte hodnoty.
   
   * Azure typ připojení – vyberte **Azure Resource Manageru**
   * Předplatné Azure RM - vyberte předplatné, pro nasazení v **předplatné Azure** rozevíracího seznamu. Obvykle to bude stejné předplatné použité v předchozím kroku
   * Akce – vyberte **vytvořit nebo aktualizovat skupinu prostředků.**
   * Skupina prostředků – vyberte skupinu prostředků, nebo zadejte název nové skupiny prostředků pro nasazení
   * Umístění, vyberte umístění pro skupinu prostředků
   * Šablona – zadejte cestu a název šablony, které mají být nasazeny předřazení **$(Build.StagingDirectory)**, například: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Parametry šablony – zadejte cestu a název parametrů, kterou chcete použít předřazení **$(Build.StagingDirectory)**, například: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Přepsání parametrů šablony – zadejte nebo zkopírujte a vložte následující kód:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Konfigurace úkol nasazení skupiny prostředků Azure][17]
7. Po přidání všechny požadované položky, uložit kanálu sestavení a zvolte **nové sestavení do fronty** v horní části.

## <a name="next-steps"></a>Další postup
Čtení [přehled Azure Resource Manageru](azure-resource-manager/resource-group-overview.md) získat další informace o Azure Resource Manageru a skupin prostředků Azure.

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
