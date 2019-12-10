---
title: Nasazení Azure FarmBeats
description: Tento článek popisuje, jak nasadit Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 27bf62cb328273db1f7bdd44117853b00feca9ae
ms.sourcegitcommit: b5ff5abd7a82eaf3a1df883c4247e11cdfe38c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74941572"
---
# <a name="deploy-azure-farmbeats"></a>Nasazení Azure FarmBeats

Tento článek popisuje, jak nastavit Azure FarmBeats.

Azure FarmBeats je rozšiřitelné a rozšiřitelné řešení pro zajištění řízené daty, které umožňuje bezproblémové zřizování a připojení zařízení k cloudu Azure, shromažďování dat telemetrie a agregaci. Azure FarmBeats zahrnuje řadu senzorů, jako jsou kamery, DRONY zachraňují životy a snímače půdy. Pomocí Azure FarmBeats můžete spravovat zařízení z cloudu, což zahrnuje správu infrastruktury a služeb v Azure pro zařízení s podporou IoT a používání rozšiřitelné webové a mobilní aplikace, která poskytuje vizualizaci, výstrahy a přehledy.

> [!NOTE]
> Azure FarmBeats se podporuje jenom ve veřejných cloudových prostředích. Další informace najdete v tématu [co je veřejný cloud?](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats má tyto dvě komponenty:

- **DataHub**: vrstva platformy Azure FarmBeats, která umožňuje sestavovat, ukládat, zpracovávat data a kreslit přehledy ze stávajících nebo nových datových kanálů. Tato vrstva platformy je užitečná ke spouštění a sestavování kanálů a modelů pro zemědělskou datovou práci.

- **Akcelerátor**: vrstva řešení Azure FarmBeats, která má vestavěnou aplikaci pro ilustraci možností Azure FarmBeats pomocí předem vytvořených zemědělských modelů. Tato vrstva řešení umožňuje vytvořit hranice farmy a vykreslovat z nich přehledy z dat o zemědělství v kontextu hranice farmy.

Rychlé nasazení služby Azure FarmBeats by nemělo trvat déle než hodinu. Náklady na DataHub a akcelerátor se liší v závislosti na využití.

## <a name="deployed-resources"></a>Nasazené prostředky

Nasazení Azure FarmBeats ve vašem předplatném vytvoří následující prostředky:

| Sér. č.  | Název prostředku  | Komponenta FarmBeats  |
|---------|---------|---------|
|1\. místo  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     DataHub/akcelerátor     |
|3  |Azure Cache for Redis   |Datahub   |
|4  |       Azure Key Vault    |  DataHub/akcelerátor        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Obor názvů centra událostí Azure    |  Datahub       |
|7  |    Azure Data Factory V2       |     DataHub/akcelerátor      |
|8  |Účet Batch    |Datahub   |
|9  |       Účet úložiště     |  DataHub/akcelerátor        |
|10  |    Aplikace logiky        |     Datahub      |
|11  |    Připojení rozhraní API        |     Datahub      |
|12|      Azure App Service      |  DataHub/akcelerátor       |
|13 |    Plán služby App Service        |     DataHub/akcelerátor      |
|14 |Účet Azure Maps     |Accelerator    |

Azure FarmBeats je k dispozici ke stažení v Azure Marketplace, ke kterému můžete přistupovat přímo z Azure Portal.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Vytvoření nabídky Azure FarmBeats v Azure Marketplace

Pokud chcete vytvořit nabídku Azure FarmBeats v Azure Marketplace, udělejte toto:

1. Přihlaste se k Azure Portal, v pravém horním rohu vyberte svůj účet a pak přepněte do tenanta Azure Active Directory (Azure AD), do kterého chcete nasadit Azure FarmBeats.
1. Azure FarmBeats je k dispozici v Azure Marketplace. Na stránce **Azure Marketplace** vyberte možnost **získat nyní**.
1. Vyberte **Create** (Vytvořit).

> [!NOTE]
> Dokončení nabídky ve Azure Marketplace je pouze součástí instalace. Pokud chcete dokončit nasazení Azure FarmBeats ve vašem předplatném Azure, postupujte podle pokynů v dalších částech.

## <a name="prepare"></a>Připravit

Než budete moct nasadit Azure FarmBeats, budete potřebovat následující oprávnění:

- **Tenant**: přístup pro čtení
- **Předplatné**: přispěvatel nebo vlastník
- **Skupina prostředků**: vlastník

## <a name="before-you-begin"></a>Než začnete

Než začnete s nasazením, zajistěte, aby byly splněné následující požadavky:

- Účet Sentinel
- Registrace aplikace Azure AD

## <a name="create-a-sentinel-account"></a>Vytvoření účtu Sentinel    

Účet s ověřovacím nástrojem Sentinel vám pomůže stáhnout si z oficiálního webu do svého zařízení. Pokud chcete vytvořit bezplatný účet, udělejte toto:

1. Přejít na [stránku registrace účtu Sentinel](https://scihub.copernicus.eu/dhus/#/self-registration).
1. V registračním formuláři zadejte své křestní jméno, příjmení, uživatelské jméno, heslo a e-mailovou adresu.

Ověřovací e-mail se pošle na registrovanou e-mailovou adresu pro potvrzení. Kliknutím na odkaz potvrďte svoji e-mailovou adresu. Proces registrace je dokončený.

## <a name="create-an-azure-ad-app-registration"></a>Vytvoření registrace aplikace Azure AD

K ověřování a autorizaci v Azure FarmBeats musíte mít registraci aplikace Azure AD. Můžete ho vytvořit jedním ze dvou způsobů:

* **Možnost 1**: Instalační program může vytvořit registraci automaticky za předpokladu, že máte požadovaná přístupová oprávnění tenanta, předplatného a skupiny prostředků. Pokud jsou tato oprávnění zavedena, pokračujte do části ["Příprava souboru Input. JSON"](#prepare-the-inputjson-file) .

* **Možnost 2**: registraci můžete před nasazením Azure FarmBeats vytvořit a nakonfigurovat ručně. Tuto metodu doporučujeme, pokud nemáte požadovaná oprávnění k vytvoření a konfiguraci registrace aplikace Azure AD v rámci vašeho předplatného. Požádejte správce, aby používal [vlastní skript](https://aka.ms/FarmBeatsAADScript), který správci IT pomůže automaticky vygenerovat a nakonfigurovat registraci aplikace Azure AD na Azure Portal. Jako výstup pro spuštění tohoto vlastního skriptu pomocí prostředí PowerShell musí správce IT sdílet s vámi ID klienta aplikace Azure AD a tajný klíč hesla. Poznamenejte si tyto hodnoty.

    Pokud chcete spustit registrační skript aplikace Azure AD, udělejte toto:

    1. Stáhněte si [skript](https://aka.ms/FarmBeatsAADScript).
    1. Přihlaste se k Azure Portal a vyberte své předplatné a tenanta Azure AD.
    1. Na panelu nástrojů v horní části Azure Portal spusťte Cloud Shell.

        ![FarmBeats projektu](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Pokud jste prvním uživatelem, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure soubory. Vyberte **Create storage** (Vytvořit úložiště).
    1. Uživatelé s prvními možnostmi nabízejí také možnosti prostředí, bash nebo PowerShell. Vyberte PowerShell.
    1. Nahrajte skript (z kroku 1) do Cloud Shell a poznamenejte si umístění nahraného souboru.

        > [!NOTE]
        > Ve výchozím nastavení se soubor nahraje do domovského adresáře.

        Použijte následující skript:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Poznamenejte si ID aplikace a tajný klíč klienta služby Azure AD, abyste sdíleli osobu, která nasazuje službu Azure FarmBeats.

### <a name="prepare-the-inputjson-file"></a>Příprava souboru Input. JSON

V rámci instalace vytvořte soubor *input. JSON* , jak je znázorněno zde:

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":" dummy-test-acc1",
    "acceleratorWebsiteName":" dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Tento soubor je vstupním souborem pro Azure Cloud Shell. Obsahuje parametry, jejichž hodnoty se při instalaci používají. Všechny parametry v souboru JSON se musí nahradit odpovídajícími hodnotami nebo odebrat. Tím se odeberou, instalační program vás během instalace zobrazí.

Před přípravou souboru zkontrolujte parametry v následující tabulce:

|Příkaz | Popis|
|--- | ---|
|skj  | Nabízí možnost stáhnout jednu nebo obě součásti služby Azure FarmBeats. Určuje, které součásti se mají stáhnout. Pokud chcete nainstalovat jenom DataHub, použijte "onlydatabhub". Pro instalaci DataHub a akcelerátoru použijte "obojí".|
|subscriptionId | Určuje předplatné pro instalaci Azure FarmBeats.|
|datahubResourceGroup| Název skupiny prostředků pro prostředky DataHub.|
|location |Umístění, kde chcete vytvořit prostředky.|
|acceleratorWebsiteName |Jedinečná předpona adresy URL pro pojmenování instance DataHub|
|acceleratorResourceGroup  | Jedinečná předpona adresy URL pro pojmenování webu akcelerátoru|
|datahubWebsiteName  | Jedinečná předpona adresy URL pro pojmenování webu DataHub |
|sentinelUsername | Uživatelské jméno pro přihlášení (například `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | E-mailová adresa pro příjem oznámení pro všechny výstrahy, které nakonfigurujete v rámci vaší instance DataHub.|
|updateIfExists| Volitelné Parametr, který se má zahrnout do souboru *input. JSON* jenom v případě, že chcete upgradovat stávající instanci služby Azure FarmBeats. V případě upgradu musí být další podrobnosti, například názvy skupin prostředků a umístění, stejné.|
|aadAppClientId | Volitelné Parametr, který se má zahrnout do souboru *input. JSON* pouze v případě, že aplikace Azure AD již existuje.  |
|aadAppClientSecret  | Volitelné Parametr, který se má zahrnout do souboru *input. JSON* pouze v případě, že aplikace Azure AD již existuje.|

## <a name="deploy-the-app-within-cloud-shell"></a>Nasazení aplikace v rámci Cloud Shell

Jako součást dříve popsaného pracovního postupu na webu Marketplace musíte mít vytvořenou jednu skupinu prostředků a podepsali si smlouvu s licenčními podmínkami, kterou můžete zkontrolovat znovu v rámci skutečného nasazení. Aplikaci můžete nasadit prostřednictvím rozhraní příkazového řádku založeného na prohlížeči Cloud Shell pomocí prostředí bash. Pokud chcete nasadit prostřednictvím Cloud Shell, pokračujte k dalším oddílům.

> [!NOTE]
> Po 20 minutách vyprší platnost neaktivních relací Cloud Shell. Zkuste nasazení dokončit v tuto chvíli.

1. Přihlaste se k Azure Portal a vyberte předplatné a tenanta Azure, které chcete použít.
1. Na panelu nástrojů v horní části Azure Portal spusťte Cloud Shell.
1. Pokud používáte Cloud Shell poprvé, budete vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Azure Files.
1. Vyberte **vytvořit úložiště**.  
1. Pro prostředí vyberte **bash** (ne PowerShell).

## <a name="deployment-scenario-1"></a>Scénář nasazení 1

V tomto scénáři, který je popsaný výše v části "možnost 1", instalační služba vytvoří registraci aplikace Azure AD automaticky. FarmBeats nasadíte pomocí následujícího postupu:

1. Zkopírujte následující vzorovou šablonu JSON a uložte ji jako *input. JSON*. Nezapomeňte si poznamenat cestu k souboru na místním počítači.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Otevřete Azure Cloud Shell. Po úspěšném ověření vyberte tlačítko **nahrát** (zvýrazněné na následujícím obrázku) a pak soubor *input. JSON* nahrajte do Cloud Shell úložiště.  

    ![FarmBeats projektu](./media/prepare-for-deployment/bash-2-1.png)

1. V Cloud Shell přejdete do svého domovského adresáře. Ve výchozím nastavení je název adresáře */home/\<uživatelského jména >* .
1. V Cloud Shell zadejte následující příkaz. Nezapomeňte změnit cestu k souboru *input. JSON* a pak vybrat klávesu ENTER.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Instalační program automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení. Budete vyzváni k vyjádření souhlasu s licenčními podmínkami Azure FarmBeats.

     - Pokud souhlasíte, přejděte k dalšímu kroku zadáním **Y** .
     - Pokud nesouhlasíte, zadejte **N**a nasazení se ukončí.

1. Na příkazovém řádku zadejte přístupový token pro nasazení. Zkopírujte vygenerovaný kód a přihlaste se k přihlašovací [stránce zařízení](https://microsoft.com/devicelogin) pomocí svých přihlašovacích údajů Azure.

    > [!NOTE]
    > Platnost tokenu vyprší po 60 minutách. V případě vypršení platnosti můžete restartovat pomocí příkazu pro nasazení.

1. Na příkazovém řádku zadejte heslo účtu Sentinel.

   Instalační program ověří a spustí nasazení, což může trvat přibližně 20 minut.

   Po úspěšném dokončení nasazení obdržíte následující výstupní odkazy:

    - **DATAHUB URL**: odkaz Swagger pro vyzkoušení rozhraní API Azure FarmBeats.
    - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy Azure FarmBeats.
    - **Soubor protokolu**nástroje pro nasazení: soubor protokolu, který je vytvořený během nasazování. V případě potřeby ho můžete použít k řešení potíží.

## <a name="deployment-scenario-2"></a>Scénář nasazení 2

V tomto scénáři, který je popsaný výše v "možnosti 2", můžete použít stávající registraci aplikace Azure Active Directory k nasazení FarmBeats pomocí následujícího postupu:

1. Zkopírujte následující soubor JSON, který obsahuje ID a heslo klienta aplikace Azure v souboru *input. JSON* a uložte ho. Nezapomeňte si poznamenat cestu k souboru na místním počítači.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

1. Znovu přejít na Azure Cloud Shell a úspěšně jste ověřili.
1. Vyberte tlačítko **nahrát** (zvýrazněné na následujícím obrázku) a pak nahrajte soubor *input. JSON* do Cloud Shell úložiště.

    ![FarmBeats projektu](./media/prepare-for-deployment/bash-2-1.png)

1. V Cloud Shell přejdete do svého domovského adresáře. Ve výchozím nastavení je název adresáře */home/\<uživatelského jména >* .
1. V Cloud Shell zadejte následující příkaz. Nezapomeňte změnit cestu k souboru *input. JSON* a pak vybrat klávesu ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     Instalační program automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení. Budete vyzváni k vyjádření souhlasu s licenčními podmínkami Azure FarmBeats.

     - Pokud souhlasíte, přejděte k dalšímu kroku zadáním **Y** .
     - Pokud nesouhlasíte, zadejte **N**a nasazení se ukončí.

1. Na příkazovém řádku zadejte přístupový token pro nasazení. Zkopírujte vygenerovaný kód a přihlaste se k přihlašovací [stránce zařízení](https://microsoft.com/devicelogin) pomocí svých přihlašovacích údajů Azure.

   Instalační program ověří a spustí nasazení, což může trvat přibližně 20 minut.

   Po úspěšném dokončení nasazení obdržíte následující výstupní odkazy:

    - **DATAHUB URL**: odkaz Swagger pro vyzkoušení rozhraní API Azure FarmBeats.
    - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy Azure FarmBeats.
    - **Soubor protokolu**nástroje pro nasazení: soubor protokolu, který je vytvořený během nasazování. V případě potřeby ho můžete použít k řešení potíží.

Pokud narazíte na nějaké problémy, přečtěte si téma [řešení potíží](troubleshoot-project-farmbeats.md).


## <a name="validate-the-deployment"></a>Ověření nasazení

### <a name="datahub"></a>Datahub

Po dokončení instalace DataHub obdržíte adresu URL pro přístup k rozhraním API Azure FarmBeats prostřednictvím rozhraní Swagger ve formátu https://\<yourdatahub-web-název >. azurewebsites. NET/Swagger.

1. Pokud se chcete přihlásit přes Swagger, zkopírujte a vložte adresu URL do prohlížeče.
2. Přihlaste se pomocí přihlašovacích údajů pro Azure Portal.
3. Můžete si prohlédnout Swagger a provádět všechny operace REST na rozhraních API Azure FarmBeats. To indikuje úspěšné nasazení služby Azure FarmBeats.

### <a name="accelerator"></a>Accelerator

Po dokončení instalace akcelerátoru obdržíte adresu URL pro přístup k uživatelskému rozhraní Azure FarmBeats ve formátu https://\<akcelerátor – název webu >. azurewebsites. NET.

1. Pokud se chcete přihlásit z akcelerátoru, zkopírujte a vložte adresu URL do prohlížeče.
1. Přihlaste se pomocí přihlašovacích údajů pro Azure Portal.

## <a name="upgrade"></a>Aktualizace

Pokyny pro upgrade jsou podobné jako při první instalaci. Udělejte toto:

1. Přihlaste se k Azure Portal a pak vyberte požadované předplatné a tenanta Azure.
1. V horním navigačním panelu Azure Portal otevřete Cloud Shell.

   ![FarmBeats projektu](./media/prepare-for-deployment/navigation-bar-1.png)

1. V rozevíracím seznamu **prostředí** vlevo vyberte **bash**.
1. V případě potřeby proveďte změny ve *vstupním souboru. JSON* a nahrajte ho do Cloud Shell. Můžete například aktualizovat svou e-mailovou adresu pro oznámení, které chcete dostávat.
1. Do Cloud Shell zadejte nebo vložte následující dva příkazy. Nezapomeňte upravit cestu k souboru *input. JSON* a pak vybrat klávesu ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   Instalační program vás automaticky vyzve k zadání požadovaných vstupů za běhu.

1. Zadejte přístupový token pro nasazení. Zkopírujte vygenerovaný kód a přihlaste se k přihlašovací [stránce zařízení](https://microsoft.com/devicelogin) pomocí svých přihlašovacích údajů Azure.
1. Zadejte heslo Sentinel.

   Instalační program teď ověří a spustí vytváření prostředků, což může trvat asi 20 minut.

   Po úspěšném dokončení nasazení obdržíte následující výstupní odkazy:
    - **DATAHUB URL**: odkaz Swagger pro vyzkoušení rozhraní API Azure FarmBeats.
    - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy Azure FarmBeats.
    - **Soubor protokolu**nástroje pro nasazení: soubor protokolu, který je vytvořený během nasazování. V případě potřeby ho můžete použít k řešení potíží.

> [!NOTE]
> Poznamenejte si předchozí hodnoty pro budoucí použití.


## <a name="uninstall"></a>Odinstalace

V současné době nepodporujeme automatizované odinstalaci Azure FarmBeats pomocí instalačního programu. Pokud chcete odebrat DataHub nebo akcelerátor, odstraňte v Azure Portal skupinu prostředků, ve které jsou tyto komponenty nainstalované, nebo tyto prostředky odstraňte ručně.

Pokud jste například nasadili DataHub a akcelerátor ve dvou různých skupinách prostředků, můžete tyto skupiny prostředků odstranit následujícím způsobem:

1. Přihlaste se k portálu Azure.
1. V pravém horním rohu vyberte svůj účet a přepněte se do tenanta Azure AD, kde chcete nasadit Azure FarmBeats.

   > [!NOTE]
   > DataHub je nutné, aby akcelerátor správně fungoval. Nedoporučujeme odDatahub odinstalovat, aniž byste taky odinstalovali akcelerátor.

1. Vyberte **skupiny prostředků**a potom zadejte název skupiny prostředků DataHub nebo akcelerátoru, kterou chcete odstranit.
1. Vyberte název skupiny prostředků. Zadejte název znovu a potvrďte ho a pak vyberte **Odstranit** . tím odeberete skupinu prostředků a všechny její podkladové prostředky.
   Případně můžete každý prostředek odstranit ručně, což je metoda, kterou nedoporučujeme.
1. Pokud chcete odstranit nebo odinstalovat DataHub, přečtěte si do skupiny prostředků přímo v Azure a pak z ní odstraňte skupinu prostředků.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nasadit Azure FarmBeats. Dále se naučíte, jak [vytvořit FarmBeats farmy](manage-farms.md#create-farms).
