---
title: Nasazení Azure FarmBeats
description: Popisuje, jak nasadit FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c609285b727414b4849c9ef6654406a035005bb1
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797722"
---
# <a name="deploy-farmbeats"></a>Nasazení FarmBeats

Tento článek popisuje, jak nastavit Azure FarmBeats.

Azure FarmBeats je konkrétní rozšiřitelné řešení pro zajištění řízené daty, které umožňuje bezproblémové zřizování a zařízení snímače připojení ke cloudu Azure, shromažďování dat telemetrie a agregaci. Azure FarmBeats má různé senzory, jako jsou kamery, DRONY zachraňují životy, půdní senzory a Správa zařízení z cloudu, což zahrnuje infrastrukturu a služby v Azure pro zařízení s podporou IoT (Internet věcí) do extendible webové a mobilní aplikace, která poskytuje. vizualizace, výstrahy a přehledy.

> [!NOTE]
> Azure FarmBeats se podporuje jenom ve veřejných cloudových prostředích. Další informace o cloudovém prostředí najdete v tématu [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/).

Azure FarmBeats má tyto dvě komponenty:

- **Data hub** – datový rozbočovač je vrstva platformy Azure FarmBeats, která umožňuje sestavovat, ukládat, zpracovávat data a vykreslovat přehledy ze stávajících nebo nových datových kanálů. Tato vrstva platformy je užitečná ke spouštění a sestavování kanálů a modelů pro zemědělskou datovou práci.

- Akcelerátor **akcelerátoru** je vrstva řešení Azure FarmBeats, která má vestavěnou aplikaci k ilustraci možností Azure FarmBeats pomocí předem vytvořených zemědělských modelů. Tato vrstva řešení umožňuje vytvořit hranice farmy a vykreslovat z nich přehledy z dat o zemědělství v kontextu hranice farmy.

Rychlé nasazení služby Azure FarmBeats by nemělo trvat déle než hodinu. Náklady na datové centrum a akcelerátor se liší v závislosti na využití.

## <a name="deployed-resources"></a>Nasazené prostředky

Nasazení Azure FarmBeats vytvoří níže uvedené prostředky v rámci vašeho předplatného:

|Zdroj č.  |Název prostředku  |Komponenta Azure FarmBeats  |
|---------|---------|---------|
|1\. místo  |       Databáze Azure Cosmos   |  Datové centrum       |
|2  |    Application Insights      |     Datové centrum/akcelerátor     |
|3  |Azure Cache for Redis   |Datové centrum   |
|4  |       Trezor klíčů Azure    |  Datové centrum/akcelerátor        |
|5  |    Time Series Insights       |     Datové centrum      |
|6 |      Obor názvů EventHub    |  Datové centrum       |
|7  |    Azure Data Factory V2       |     Datové centrum/akcelerátor      |
|8  |Účet Batch    |Datové centrum   |
|9  |       Účet úložiště     |  Datové centrum/akcelerátor        |
|10  |    Aplikace logiky        |     Datové centrum      |
|11  |    Připojení rozhraní API        |     Datové centrum      |
|12|      App Service      |  Datové centrum/akcelerátor       |
|13 |    plán služby App Service        |     Datové centrum/akcelerátor      |
|14 |Účet Azure Maps     |Accelerator    |
|15 |       Time Series Insights      |  Datové centrum     |

Azure FarmBeats je k dispozici pro stažení z Azure Marketplace. K němu můžete přistupovat přímo z Azure Portal.  

## <a name="prepare"></a>Příprava

Pro nasazení Azure FarmBeats potřebujete následující oprávnění:

- Tenant: přístup pro čtení
- Předplatné: přispěvatel na vlastníka
- Skupina prostředků: vlastník

## <a name="before-you-begin"></a>Než začnete

Před zahájením nasazení se ujistěte, že máte následující:

- Účet Sentinel
- Azure Active Directory (registrace aplikace)
- FarmBeats Azure

## <a name="create-a-sentinel-account"></a>Vytvoření účtu Sentinel    

Účet s ověřovacím nástrojem Sentinel vám pomůže stáhnout si z oficiálního webu do svého zařízení. Pomocí těchto kroků můžete vytvořit bezplatný účet:

1. Přejděte do části https://scihub.copernicus.eu/dhus/#/self-registration (Soubor > Nový > Jiné). Na stránce registrace zadejte jméno, příjmení, uživatelské jméno, heslo a e-mail.
2. V rozevírací nabídce **Vybrat doménu** vyberte možnost **půda**.
3. V rozevírací nabídce **Vybrat využití** vyberte možnost **vzdělání**.
4. V rozevírací nabídce **vybrat zemi** vyberte svou zemi.
5. Pro dokončení procesu registrace vyberte **Registrovat** .

Ověřovací e-mail se pošle na registrovanou e-mailovou adresu pro potvrzení. Vyberte odkaz a potvrďte ho. Proces registrace je dokončený.

## <a name="create-azure-ad-app-registration"></a>Vytvořit registraci aplikace Azure AD

K ověřování a autorizaci v Azure FarmBeats musíte mít registraci aplikace Azure Active Directory, která:

- Případ 1: Instalační program se může vytvořit automaticky (Pokud máte požadovaná přístupová oprávnění tenanta, předplatného a skupiny prostředků).
- Případ 2: před nasazením Azure FarmBeats můžete vytvořit a nakonfigurovat (vyžaduje ruční kroky).

**Případ 1**: Instalační program předpokládá, že máte práva k vytvoření registrace aplikace v Azure Active Directory v rámci požadovaného předplatného. Pokud se chcete zaregistrovat, přihlaste se k portálu, v **Azure Active directory** > **registraci aplikace** > **Nová registrace**.

Pokud už předplatné máte, můžete ho přímo přesunout na další postup.

**Případ 2**: Tato metoda je preferovaný krok, pokud nemáte dostatečná oprávnění k vytvoření a konfiguraci registrace aplikace Azure AD v rámci vašeho předplatného. Požádejte správce, aby používal [vlastní skript](https://aka.ms/FarmBeatsAADScript), který správci IT pomůže automaticky vygenerovat a nakonfigurovat registraci aplikace Azure AD na Azure Portal. Jako výstup pro spuštění tohoto vlastního skriptu pomocí prostředí PowerShell musí správce IT sdílet s vámi Azure Active Directory ID klienta aplikace a tajné heslo. Poznamenejte si tyto hodnoty.

Ke spuštění registračního skriptu aplikace služby Azure AD použijte následující postup:

1. Získejte registrační [skript](https://aka.ms/FarmBeatsAADScript).
2. Přihlaste se k Azure Portal a vyberte své předplatné a tenanta služby AD.
3. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.

    ![Beats farmy projektu](./media/prepare-for-deployment/navigation-bar-1.png)


4. Uživatelé budou při prvním spuštění vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure soubory. Vyberte **Create storage** (Vytvořit úložiště).
5. Při prvním zobrazení se uživatelům zobrazí výzva s výběrem preferovaného prostředí prostředí – bash nebo PowerShellu. Vyberte PowerShell.
6. V Cloud Shell zadejte níže uvedené příkazy ke spuštění skriptu.

    ```powershell
    ./create_aad_script.ps1
    ```
7. Poznamenejte si ID aplikace a tajný klíč klienta služby Azure AD, abyste mohli sdílet s osobou nasazující Azure FarmBeats.

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Vytvoření nabídky Azure FarmBeats na webu Marketplace

Pomocí těchto kroků můžete na webu Marketplace vytvořit nabídku Azure FarmBeats:

1. Přihlaste se k **Azure Portal** a v pravém horním rohu vyberte svůj účet a přepněte se do TENANTA Azure AD, kde chcete nasadit Microsoft Azure FarmBeats.
2. Vyberte **Hledat, Marketplace** nebo **vytvořit prostředky**. Zadáním **FarmBeats** zobrazíte podrobnosti nabídky. Než budete pokračovat k dalším krokům, Stáhněte si příručky na této stránce, které jsou k dispozici prostřednictvím odkazů aka.ms.
3. Vyberte **vytvořit** a zadejte následující informace:

   - Zadejte název předplatného.
   - Zadejte existující název skupiny prostředků (jenom prázdné skupiny prostředků) nebo vytvořte novou skupinu prostředků pro nasazení služby Azure FarmBeats. Poznamenejte si tuto skupinu prostředků, kterou chcete v pozdější fázi zadat v souboru Input. JSON.
   - Zadejte oblast, do které chcete službu Azure FarmBeats nainstalovat. FarmBeats se teď dají nainstalovat do těchto oblastí: Střed USA, Západní Evropa, Východní USA 2, Severní Evropa, Západní USA, jihovýchodní Asie, Východní USA, Austrálie – východ, Západní USA 2.
4. Vyberte **OK**, které vás přesměruje na stránku podmínky použití. Přečtěte si standardní údaje na webu Marketplace nebo vyberte hypertextový odkaz pro kontrolu podmínek použití.
5. Vyberte **Zavřít**, potom zaškrtněte políčko Souhlasím a pak vyberte **vytvořit**.
6. Na webu Marketplace jste teď úspěšně zaregistrovali licenční smlouvu s koncovým uživatelem (EULA) pro Azure FarmBeats. Pokud chcete pokračovat v nasazení, postupujte podle dalších kroků v této příručce.

### <a name="prepare-inputjson-file"></a>Příprava souboru Input. JSON

Tento soubor je vstupním souborem, který Azure Cloud Shell a parametry, jejichž hodnoty jsou zadané v tomto souboru, ještě než se během nasazování neobjeví výzva, takže vám ušetří čas.  

> [!NOTE]
> Tyto hodnoty vstupů do souboru Azure Cloud Shell.  Pokud chcete ušetřit čas během nasazování, nebudete vyzváni k zadání parametrů, které přidáte do tohoto souboru. Zobrazí se výzva k zadání nezmeškaných parametrů.

Před přípravou souboru zkontrolujte parametry.

|Příkaz | Popis|
|--- | ---|
|skladové  | Nabízí možnost stáhnout buď součásti Azure FarmBeats, nebo obě. Určuje, které součásti se mají stáhnout. K instalaci pouze datového centra použijte "onlydatabhub". K instalaci datového centra a akcelerátoru použijte obojí.|
|SubscriptionId  | Určuje předplatné pro instalaci FarmBeats.|
|"datahubResourceGroup"  | Název skupiny prostředků pro prostředky datového centra.|
|"datahubLocation" | Místo, kam chcete uložit prostředky datového centra.|
|"acceleratorWebsiteName"  |Jedinečná předpona adresy URL pro pojmenování datového centra
Web Swagger Výchozí hodnota je název skupiny prostředků datového centra. Pokud chcete pokračovat s výchozí hodnotou, stiskněte klávesu ENTER.|
|"acceleratorResourceGroup"  | Název skupiny prostředků pro prostředky datového centra. |
|"acceleratorLocation"  | Umístění pro ukládání prostředků datového centra
"acceleratorWebsiteName"  | Jedinečná předpona adresy URL pro pojmenování webu akcelerátoru Výchozí hodnota je akcelerátor. Pokud chcete pokračovat s výchozí hodnotou, stiskněte klávesu ENTER.|
|''sentinelUsername'' | uživatelské jméno pro přihlášení: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"sentinelPassword"  | Heslo pro přihlášení: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"farmbeatsAppId"  | Hodnoty, které mají být sdíleny týmem Azure FarmBeats.  |
|"farmbeatsPassword"  | Hodnoty, které mají být sdíleny týmem Azure FarmBeats.|
|"notificationEmailAddress"  | E-mailová adresa pro příjem oznámení pro všechny výstrahy, které nakonfigurujete v rámci datového centra.|
|"kterákoli" aadAppClientId ""  |[**Nepovinné**] Parametr, který se má zahrnout do Input. JSON jenom v případě, že aplikace Azure AD už existuje  – True nebo false. Hodnota false pro první instalaci a pro scénář upgradu true.|
|"aadAppClientId"  | [**Nepovinné**] Parametr, který se má zahrnout do Input. JSON jenom v případě, že aplikace Azure AD už existuje  |
|"aadAppClientSecret"   | [**Nepovinné**] Parametr, který se má zahrnout do Input. JSON jenom v případě, že aplikace Azure AD už existuje|


Ukázka vstupu JSON:

```json
{  
   "sku":"both", 
   "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
   "datahubResourceGroup": "dummy-test-dh1", 
   "datahubLocation": "westus2", 
   "datahubWebsiteName": "dummy-test-dh1", 
   "acceleratorResourceGroup": "dummy-test-acc1", 
   "acceleratorLocation": "westus2", 
   "acceleratorWebsiteName": "dummy-test-acc1", 
   "sentinelUsername": "dummy-dev", 
   "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
   "notificationEmailAddress": "dummy@microsoft.com", 
   "updateIfExists": true
}
```
## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Nasazení v rámci Cloud Shell příkazového řádku založeného na prohlížeči

Jako součást pracovního postupu Marketplace jste vytvořili jednu skupinu prostředků a podepsali si licenční smlouvu s koncovým uživatelem, kterou je možné znovu zkontrolovat v rámci skutečného nasazení. Nasazení bude provedeno prostřednictvím Azure Cloud Shell (příkazový řádek založený na prohlížeči) pomocí prostředí bash.  

> [!NOTE]
> Po 20 minutách vyprší platnost neaktivních relací Cloud Shell. Zkuste nasazení dokončit v tuto chvíli.

1. Přihlaste se k portálu **Azure** Portal a vyberte požadované předplatné a tenanta služby AD.
2. Spusťte **Cloud Shell** v horním navigačním panelu webu **Azure** Portal.

   ![Beats farmy projektu](./media/prepare-for-deployment/navigation-bar-1.png)

3. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště a sdílenou složku Microsoft Azure Files.
4. Vyberte **Create storage** (Vytvořit úložiště).
5. V levé části okna prostředí vyberte rozevírací nabídku prostředí (bash).

   ![Beats farmy projektu](./media/prepare-for-deployment/bash-1-1.png)

### <a name="deployment-scenario-1"></a>Scénář nasazení 1

Instalační program vytvoří službu Azure AD (máte oprávnění ke čtení tenanta AD).  

1. Stáhněte šablonu [input. JSON](https://aka.ms/PPInputJsonTemplate) . Do souboru Input. JSON zadejte ID a heslo aplikace Azure a uložte je.
2. Otevřete stažený soubor v poznámkovém bloku a naplňte ho zadáním hodnot.

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
    "updateIfExists":true
    }

    ```

    > [!NOTE]
    > Tento krok můžete přeskočit a vstupy budou zobrazovat výzvu za běhu.

3. Uložte soubor a poznamenejte si cestu (na místním počítači).  
4. Přejděte na Azure Cloud Shell a po úspěšném ověření vyberte nahrávání (viz Zvýrazněná ikona na obrázku níže) a nahrajte soubor Input. JSON do úložiště Cloud Shell. Nemusíte předat parametr Azure AD ve formátu JSON, protože instalační program vytvoří a nakonfiguruje pro vás registraci aplikace Azure AD.

   ![Beats farmy projektu](./media/prepare-for-deployment/bash-2-1.png)

5. Do Cloud Shell zadejte nebo vložte příkaz pro nasazení. Nezapomeňte změnit cestu k zadání. Soubor JSON a stiskněte klávesu ENTER.  

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Skript automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení. Pak budete vyzváni k vyjádření souhlasu s licenční smlouvou s koncovým uživatelem (EULA) pro Azure FarmBeats.

   - Pokud souhlasíte a přejdete k dalšímu kroku, zadejte ' Y '.
   - Pokud s podmínkami nesouhlasíte a nasazení skončí, zadejte N.

   Pak se zobrazí výzva k zadání přístupového tokenu pro nasazení. Zkopírujte kód, který jste vygenerovali, a přihlaste se https://microsoft.com/devicelogin s přihlašovacími údaji Azure.

   > [!NOTE]
   > Platnost kódu vyprší po 60 minutách. Po vypršení platnosti můžete znovu zadat příkaz pro nasazení.

6. Do dalšího řádku zadejte heslo účtu Sentinel.
7. Instalační program nyní ověřuje a spouští nasazení, což může trvat přibližně 20 minut.
8. Po úspěšném nasazení se zobrazí následující výstupní odkazy:
    - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní API Azure FarmBeats
    - **Adresa URL akcelerátoru**: uživatelské rozhraní pro prozkoumání akcelerátoru inteligentní farmy Azure FarmBeats.
    - **Soubor protokolu nástroje Deploy**– soubor protokolu vytvořený během nasazování. Dá se použít k řešení potíží.

    - Pokud souhlasíte a budete pokračovat k dalšímu kroku, zadejte ' Y '.
    - Zadejte N, pokud s podmínkami nesouhlasíte a nasazení skončí.

   Pak se zobrazí výzva k zadání přístupového tokenu pro nasazení. Zkopírujte generovaný kód a přihlaste se k https://microsoft.com/devicelogin s přihlašovacími údaji Azure.

   > [!NOTE]
    > Poznamenejte si je a nechte cestu k souboru protokolu nasazení užitečnou pro budoucí použití.


### <a name="deployment-scenario-2"></a>Scénář nasazení 2

K nasazení se používá existující registrace aplikace Azure Active Directory.

1. [Vstupní JSON](https://aka.ms/PPInputJsonTemplate) ke stažení obsahuje ID a heslo Azure Application ID klienta v souboru Input. JSON.

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

     Postupujte podle zbývajících kroků:

2. Poznamenejte si cestu k souboru Input. JSON (na místním počítači).
3. Znovu přejděte na Azure Cloud Shell a úspěšně jste ověřili, vyberte tlačítko nahrát (viz Zvýrazněná ikona na obrázku níže) a nahrajte soubor Input. JSON do úložiště Cloud Shell.

    ![Beats farmy projektu](./media/prepare-for-deployment/bash-2-1.png)

4. Do Cloud Shell zadejte nebo vložte *příkaz pro nasazení* . Nezapomeňte změnit cestu k zadání. Soubor JSON a stiskněte klávesu ENTER.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
   Postupujte podle zbývajících kroků:

5. Skript automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení.
6. Zobrazí se výzva ke čtení a vyjádření souhlasu s licenční smlouvou s koncovým uživatelem (EULA) pro Azure FarmBeats.

   - Pokud souhlasíte a budete pokračovat k dalšímu kroku, zadejte ' Y '.
   - Zadejte N, pokud s podmínkami nesouhlasíte a nasazení skončí.

7. Zobrazí se výzva k zadání přístupového tokenu pro nasazení. Zkopírujte generovaný kód a přihlaste se k https://microsoft.com/devicelogin s přihlašovacími údaji Azure.
8. Instalační program nyní ověří a spustí vytváření prostředků, což může trvat přibližně 20 minut. Během této doby zůstane relace v Cloud Shell aktivní.
9. Po úspěšném nasazení se zobrazí níže uvedené výstupní odkazy:
   - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní FarmBeats API
   - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy FarmBeats
   - **Soubor protokolu**nástroje pro nasazení: uloží protokoly během nasazování a dá se použít k řešení potíží.

Pokud narazíte na nějaké problémy, přečtěte si téma [řešení potíží](troubleshoot-project-farmbeats.md).


## <a name="validate-deployment"></a>Ověřit nasazení

### <a name="data-hub"></a>Datové centrum

Po dokončení instalace centra dat obdržíte adresu URL pro přístup k rozhraním API služby Azure FarmBeats prostřednictvím rozhraní Swagger ve formátu: https://\<yourdatahub-website-název >. azurewebsites. NET

1. Pokud se chcete přihlásit přes Swagger, zkopírujte a vložte adresu URL do prohlížeče.
2. Přihlaste se pomocí přihlašovacích údajů Azure Portal.
3. Správnosti test (volitelné)

     - Umožňuje úspěšné přihlášení k portálu Swagger pomocí odkazu centra dat, který jste dostali jako výstup do úspěšného nasazení.
     - Rozšířené typy získat rozhraní API – vyberte vyzkoušet vyzkoušení:/Execute.
     - Měli byste obdržet kód odpovědi serveru 200 a nikoli výjimku, jako je například 403 "neoprávněný uživatel".

### <a name="accelerator"></a>Accelerator

Po dokončení instalace akcelerátoru obdržíte adresu URL pro přístup k uživatelskému rozhraní FarmBeats ve formátu: https://\<akcelerátor – název webu >. azurewebsites. NET

1. Pokud se chcete přihlásit z akcelerátoru, zkopírujte a vložte adresu URL do prohlížeče.
2. Přihlaste se pomocí přihlašovacích údajů Azure Portal.
3. Spusťte volitelný test správnosti.

    - Ověřte, jestli se můžete úspěšně přihlásit k portálu akcelerátoru pomocí odkazu akcelerátoru, který jste dostali jako výstup do úspěšného nasazení.
    - Vyberte **vytvořit farmu**.
    - Pod ikonou "?" otevřete Průvodce FarmBeats **pomocí tlačítka Začínáme** .

## <a name="upgrade"></a>Aktualizace

Postup upgradu je podobný jako při první instalaci. Postupujte následovně:

1. Přihlaste se k Azure Portal a vyberte požadované předplatné a tenanta služby AD.
2. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.

   ![Beats farmy projektu](./media/prepare-for-deployment/navigation-bar-1.png)

3. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště, a sdílené složky souborů Azure.
4. Vyberte **Create storage** (Vytvořit úložiště).
5. Z rozevírací nabídky vlevo od prostředí shell vyberte prostředí (bash).
6. Změny v souboru Input. JSON proveďte v případě potřeby a nahrajte do Azure Cloud Shell. Můžete například aktualizovat svou e-mailovou adresu pro oznámení, které chcete dostávat.
7. Poznamenejte si cestu k souboru Input. JSON (na místním počítači).
8. Přejít na Azure Cloud Shell. Po úspěšném ověření vyberte tlačítko nahrát (Zobrazit zvýrazněnou ikonu na obrázku níže) a nahrajte soubor Input. JSON do Cloud Shell úložiště.

   ![Beats farmy projektu](./media/prepare-for-deployment/bash-2-1.png)

9. Do Cloud Shell zadejte nebo vložte **příkaz pro nasazení** . Nezapomeňte změnit cestu k zadání. Soubor JSON a stiskněte klávesu ENTER.

    ```
    wget -N -O farmbeats-installer.sh https://aka.ms/FB_1.2.0 && bash farmbeats-installer.sh> /home/dummyuser/input.json

    ```
    Postupujte podle zbývajících kroků:

10. Instalační program automaticky vyzve požadované vstupy v době běhu:

    - Zadejte přístupový token pro nasazení. Zkopírujte generovaný kód a přihlaste se k https://microsoft.com/devicelogin s přihlašovacími údaji Azure.

     > [!NOTE]
     > Platnost kódu vyprší po 60 minutách. Po vypršení platnosti můžete znovu zadat příkaz pro nasazení.

     - Heslo Sentinel

11. Instalační program teď ověří a spustí vytváření prostředků, což může trvat asi 20 minut.
12. Po úspěšném nasazení se zobrazí následující výstupní odkazy:

    - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní FarmBeats API
    - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy FarmBeats
    - **Soubor protokolu**nástroje pro nasazení: uloží protokoly během nasazování. Dá se použít k řešení potíží.

    > [!NOTE]
    > Poznamenejte si výše uvedené odkazy a zachovejte cestu k souboru protokolu nasazení pro budoucí použití.

## <a name="uninstall"></a>Odinstalace

V současné době nepodporujeme automatizované odinstalaci FarmBeats pomocí instalačního programu. Chcete-li odebrat datové centrum nebo akcelerátor, odstraňte v Azure Portal skupinu prostředků, ve které jsou tyto součásti nainstalovány, nebo ručně odstraňte prostředky.

Pokud jste například nasadili datové centrum a akcelerátor ve dvou různých skupinách prostředků, odstraníte tyto skupiny prostředků následujícím způsobem:

1. Přihlaste se k Azure Portal.
2. V pravém horním rohu vyberte svůj účet a přepněte se na požadovaného tenanta Azure AD, kde chcete nasadit Microsoft FarmBeats.

   > [!NOTE]
   > Pro správné fungování akcelerátoru je potřeba datové centrum. Nedoporučujeme odinstalaci datového centra bez odinstalace akcelerátoru.

3. Vyberte skupiny prostředků a zadejte název skupiny prostředků akcelerátoru datového centra, kterou chcete odstranit.
4. Vyberte název skupiny prostředků. Opětovným zadáním názvu znovu zaškrtněte a kliknutím na Odstranit odeberte skupinu prostředků a všechny její podkladové prostředky.
5. Případně můžete každý prostředek odstranit ručně, což se nedoporučuje.
7. Pokud chcete datový rozbočovač odstranit nebo odinstalovat, přečtěte si do skupiny prostředků přímo v Azure a odstraňte skupinu prostředků z této služby.

## <a name="next-steps"></a>Další kroky

Nasadili jste Azure FarmBeats. Nyní se naučíte, jak [vytvářet farmy](manage-farms.md#create-farms).
