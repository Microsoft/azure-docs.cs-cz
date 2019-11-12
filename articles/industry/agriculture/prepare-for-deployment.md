---
title: Nasazení Azure FarmBeats
description: Popisuje, jak nasadit FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: acc64486ac8bd15a9abab1f2010ea56b752a1e86
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927674"
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
|1  |       Azure Cosmos DB   |  Datové centrum       |
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

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Vytvoření nabídky Azure FarmBeats na webu Marketplace

Pomocí těchto kroků můžete na webu Marketplace vytvořit nabídku Azure FarmBeats:

1. Přihlaste se k Azure Portal a v pravém horním rohu vyberte svůj účet a přepněte se do tenanta Azure AD, kde chcete nasadit Microsoft Azure FarmBeats.
2. Azure FarmBeats je k dispozici na adrese Azure Marketplace. Na stránce Marketplace vyberte možnost "získat IT nyní".
3. Vyberte vytvořit a zadejte následující informace:
    - název předplatného
    - existující název skupiny prostředků (jenom prázdné skupiny prostředků) nebo vytvořte novou skupinu prostředků pro nasazení služby Azure FarmBeats. Tuto skupinu prostředků si poznamenejte v dalších oddílech.
4. Oblast, do které chcete nainstalovat službu Azure FarmBeats. V současné době FarmBeats následující oblasti: Střed USA, Západní Evropa, Východní USA 2, Severní Evropa, Západní USA, jihovýchodní Asie, Východní USA, Austrálie – východ, Západní USA 2.
5. Vyberte **OK**.
Zobrazí se stránka Podmínky použití. Přečtěte si standardní údaje na webu Marketplace nebo vyberte hypertextový odkaz pro kontrolu podmínek použití.
6. Vyberte **Zavřít**, potom zaškrtněte políčko Souhlasím a pak vyberte **vytvořit**.
7. Na webu Marketplace jste teď úspěšně zaregistrovali licenční smlouvu s koncovým uživatelem (EULA) pro Azure FarmBeats.  
7. Pokud chcete pokračovat v nasazení, postupujte podle dalších kroků v této příručce.

## <a name="prepare"></a>Příprava

Pro nasazení Azure FarmBeats potřebujete následující oprávnění:

- Tenant: přístup pro čtení
- Předplatné: přispěvatel nebo vlastník
- Skupina prostředků: vlastník

## <a name="before-you-begin"></a>Než začnete

Před zahájením nasazení se ujistěte, že máte následující:

- Účet Sentinel
- Registrace aplikace Azure Active Directory (AD)

## <a name="create-a-sentinel-account"></a>Vytvoření účtu Sentinel    

Účet s ověřovacím nástrojem Sentinel vám pomůže stáhnout si z oficiálního webu do svého zařízení. Pomocí těchto kroků můžete vytvořit bezplatný účet:

Přejděte do části https://scihub.copernicus.eu/dhus/#/self-registration (Soubor > Nový > Jiné). Na stránce registrace zadejte jméno, příjmení, uživatelské jméno, heslo a e-mail.
Ověřovací e-mail se pošle na registrovanou e-mailovou adresu pro potvrzení. Vyberte odkaz a potvrďte ho. Proces registrace je dokončený.

## <a name="create-azure-ad-app-registration"></a>Vytvořit registraci aplikace Azure AD

K ověřování a autorizaci v Azure FarmBeats musíte mít registraci aplikace Azure Active Directory, která:

- Případ 1: Instalační program se může vytvořit automaticky (Pokud máte požadovaná přístupová oprávnění tenanta, předplatného a skupiny prostředků).
- Případ 2: před nasazením Azure FarmBeats můžete vytvořit a nakonfigurovat (vyžaduje ruční kroky).

**Případ 1**: Pokud máte přístup k vytvoření registrace aplikace AAD, můžete tento krok přeskočit a nechat instalační program vytvořit registraci aplikace. Pokračujte prosím na další část: [Příprava souboru Input. JSON](#prepare-input-json-file)

Pokud už předplatné máte, můžete ho přímo přesunout na další postup.

**Případ 2**: Tato metoda je preferovaný krok, pokud nemáte dostatečná oprávnění k vytvoření a konfiguraci registrace aplikace Azure AD v rámci vašeho předplatného. Požádejte správce, aby používal [vlastní skript](https://aka.ms/FarmBeatsAADScript), který správci IT pomůže automaticky vygenerovat a nakonfigurovat registraci aplikace Azure AD na Azure Portal. Jako výstup pro spuštění tohoto vlastního skriptu pomocí prostředí PowerShell musí správce IT sdílet s vámi Azure Active Directory ID klienta aplikace a tajné heslo. Poznamenejte si tyto hodnoty.

Ke spuštění registračního skriptu aplikace služby Azure AD použijte následující postup:

1. Stáhněte si [skript](https://aka.ms/FarmBeatsAADScript).
2. Přihlaste se k Azure Portal a vyberte své předplatné a tenanta služby AD.
3. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.

    ![Beats farmy projektu](./media/prepare-for-deployment/navigation-bar-1.png)


4. Uživatelé budou při prvním spuštění vyzváni k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure soubory. Vyberte **Create storage** (Vytvořit úložiště).
5. Při prvním zobrazení se uživatelům zobrazí výzva s výběrem preferovaného prostředí prostředí – bash nebo PowerShellu. Vyberte PowerShell.
6. Nahrajte skript (z kroku 1) do Cloud Shell a poznamenejte si umístění nahraného souboru.

    > [!NOTE]
    > Ve výchozím nastavení se nahraje do domovského adresáře.

    Použijte následující skript:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Poznamenejte si ID aplikace a tajný klíč klienta služby Azure AD, abyste mohli sdílet s osobou nasazující Azure FarmBeats.

### <a name="prepare-input-json-file"></a>Příprava vstupního souboru JSON

V rámci instalace vytvořte soubor Input. JSON následujícím způsobem:

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

Tento soubor je vstupním souborem, který Azure Cloud Shell a parametry, jejichž hodnoty se při instalaci používají. Všechny parametry ve formátu JSON musí být nahrazeny příslušnými hodnotami nebo odebrány. Po odebrání instalační program zobrazí výzvu během instalace.


Před přípravou souboru zkontrolujte parametry.

|Příkaz | Popis|
|--- | ---|
|skladové  | Nabízí možnost stáhnout buď součásti Azure FarmBeats, nebo obě. Určuje, které součásti se mají stáhnout. K instalaci pouze datového centra použijte "onlydatabhub". K instalaci datového centra a akcelerátoru použijte obojí.|
|subscriptionId | Určuje předplatné pro instalaci FarmBeats.|
|datahubResourceGroup| Název skupiny prostředků pro prostředky datového centra|
|location |Místo, kde chcete vytvořit prostředky|
|acceleratorWebsiteName |Jedinečná předpona adresy URL pro pojmenování datového centra|
|acceleratorResourceGroup  | Jedinečná předpona adresy URL pro pojmenování webu akcelerátoru|
|datahubWebsiteName  | UUnique prefix adresy URL pro pojmenování webu centra dat |
|sentinelUsername | uživatelské jméno pro přihlášení: https://scihub.copernicus.eu/dhus/#/self-registration.|
|notificationEmailAddress  | E-mailová adresa pro příjem oznámení pro všechny výstrahy, které nakonfigurujete v rámci datového centra.|
|updateIfExists|Volitelné Parametr, který se má zahrnout do Input. JSON jenom v případě, že chcete upgradovat existující instanci FarmBeats Pro upgrade, další podrobnosti, např. názvy skupin prostředků, umístění atd. musí být stejné.|
|aadAppClientId | [**Nepovinné**] Parametr, který se má zahrnout do Input. JSON jenom v případě, že aplikace Azure AD už existuje  |
|aadAppClientSecret  | [**Nepovinné**] Parametr, který se má zahrnout do Input. JSON jenom v případě, že aplikace Azure AD už existuje|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Nasazení v rámci Cloud Shell příkazového řádku založeného na prohlížeči

V rámci výše uvedeného pracovního postupu Marketplace musíte mít vytvořenou jednu skupinu prostředků a podepsat si licenční smlouvu s koncovým uživatelem, kterou můžete znovu zkontrolovat v rámci skutečného nasazení. Nasazení lze provést prostřednictvím Azure Cloud Shell (příkazový řádek na bázi prohlížeče) pomocí prostředí bash. Pokračujte prosím dalšími oddíly a nasaďte je přes Cloud Shell.

> [!NOTE]
> Po 20 minutách vyprší platnost neaktivních relací Cloud Shell. Zkuste nasazení dokončit v tuto chvíli.

1. Přihlaste se Azure Portal a vyberte požadované předplatné a tenanta služby AD.
2. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.
3. Pokud Cloud Shell používáte poprvé, zobrazí se výzva k výběru předplatného pro vytvoření účtu úložiště a sdílené složky Microsoft Azure souborů.
4. Vyberte **vytvořit úložiště**.  

Vyberte prostředí jako bash (ne PowerShell).

## <a name="deployment-scenario-1"></a>Scénář nasazení 1

Instalační program vytvoří registraci Aplikace Azure AD (případ 1 výše).

1. Zkopírujte následující šablonu a pojmenujte ji Input. JSON.  
Ukázka vstupu JSON:

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

2. Uložte soubor a poznamenejte si cestu (na místním počítači).
3. Přejděte na Azure Cloud Shell a po úspěšném ověření vyberte nahrávání (viz Zvýrazněná ikona na obrázku níže) a nahrajte soubor Input. JSON do úložiště Cloud Shell.  

    ![Beats farmy projektu](./media/prepare-for-deployment/bash-2-1.png)

4. Přejít do domovského adresáře ve službě cloud Shell. Ve výchozím nastavení je to/Home/<username>
5. Do Cloud Shell zadejte nebo vložte následující příkaz. Nezapomeňte změnit cestu k zadání. Soubor JSON a stiskněte klávesu ENTER.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Instalační program automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení. Zobrazí se výzva k vyjádření souhlasu s licenční smlouvou s koncovým uživatelem (EULA) pro Azure FarmBeats.

     - Pokud souhlasíte a přejdete k dalšímu kroku, zadejte ' Y '.
     - Pokud s podmínkami nesouhlasíte a nasazení skončí, zadejte N.

6. Pak se zobrazí výzva k zadání přístupového tokenu pro nasazení. Zkopírujte kód, který jste vygenerovali, a přihlaste se https://microsoft.com/devicelogin s přihlašovacími údaji Azure.

    > [!NOTE]
    > Platnost tokenu vyprší po 60 minutách. Po vypršení platnosti můžete znovu zadat příkaz pro nasazení.

7. Po zobrazení výzvy zadejte heslo účtu Sentinel.
8. Instalační program nyní ověřuje a spouští nasazení, což může trvat přibližně 20 minut.
9. Po úspěšném nasazení se zobrazí následující výstupní odkazy:

 - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní API Azure FarmBeats
 - **Adresa URL akcelerátoru**: uživatelské rozhraní pro prozkoumání akcelerátoru inteligentní farmy Azure FarmBeats.
 - **Soubor protokolu nástroje Deploy**– soubor protokolu vytvořený během nasazování. V případě potřeby se dá použít k řešení potíží.

## <a name="deployment-scenario-2"></a>Scénář nasazení 2

Pro nasazení se používá existující registrace aplikace Azure Active Directory (případ 2 výše)

1. Zkopírujte níže uvedený soubor JSON, který obsahuje ID a heslo aplikačního klienta Azure v souboru Input. JSON a uložte ho.

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

4. Přejít do domovského adresáře ve službě cloud Shell. Ve výchozím nastavení je to/Home/<username>
5. Do Cloud Shell zadejte nebo vložte následující příkaz. Nezapomeňte změnit cestu k zadání. Soubor JSON a stiskněte klávesu ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

Postupujte podle pokynů na obrazovce.

6. Skript automaticky stáhne všechny závislosti a vytvoří nástroj pro nasazení.
7. Zobrazí se výzva ke čtení a vyjádření souhlasu s licenční smlouvou s koncovým uživatelem (EULA) pro Azure FarmBeats.

    - Pokud souhlasíte a budete pokračovat k dalšímu kroku, zadejte ' Y '.
    - Zadejte N, pokud s podmínkami nesouhlasíte a nasazení skončí.

8. Zobrazí se výzva k zadání přístupového tokenu pro nasazení. Zkopírujte generovaný kód a přihlaste se k https://microsoft.com/devicelogin s přihlašovacími údaji Azure.
9. Instalační program nyní ověří a spustí vytváření prostředků, což může trvat přibližně 20 minut. Během této doby zůstane relace v Cloud Shell aktivní.
10. Po úspěšném nasazení dostanou následující výstupní odkazy:

 - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní FarmBeats API
 - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy FarmBeats
 - **Soubor protokolu**nástroje pro nasazení: soubor protokolu vytvořený během nasazování. V případě potřeby se dá použít k řešení potíží.

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

## <a name="upgrade"></a>Upgrade

Postup upgradu je podobný jako při první instalaci. Postupujte následovně:

1. Přihlaste se k Azure Portal a vyberte požadované předplatné a tenanta služby AD.
2. Spusťte Cloud Shell z horního navigačního panelu na webu Azure Portal.

   ![Beats farmy projektu](./media/prepare-for-deployment/navigation-bar-1.png)

3. Z rozevírací nabídky nalevo od prostředí shell vyberte prostředí jako "bash".
4. Změny v souboru Input. JSON proveďte v případě potřeby a nahrajte do Azure Cloud Shell. Můžete například aktualizovat svou e-mailovou adresu pro oznámení, které chcete dostávat.
5. Nahrajte soubor Input. JSON do Azure Cloud Shell.
6. Do Cloud Shell zadejte nebo vložte následující dva příkazy. Ujistěte se, že jste změnili cestu k souboru Input. JSON a stisknete klávesu ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
Postupujte podle pokynů na obrazovce:

7. Instalační program automaticky vyzve požadované vstupy v době běhu:
8. Zadejte přístupový token pro nasazení. Zkopírujte generovaný kód a přihlaste se k https://microsoft.com/devicelogin s přihlašovacími údaji Azure.
9. Heslo Sentinel
10. Instalační program teď ověří a spustí vytváření prostředků, což může trvat asi 20 minut.
11. Po úspěšném nasazení se zobrazí následující výstupní odkazy:
 - **Adresa URL centra dat**: odkaz Swagger k vyzkoušení rozhraní FarmBeats API
 - **Adresa URL akcelerátoru**: uživatelské rozhraní pro zkoumání akcelerátoru inteligentní farmy FarmBeats
 - **Soubor protokolu**nástroje pro nasazení: uloží protokoly během nasazování. Dá se použít k řešení potíží.

> [!NOTE]
> Poznamenejte si výše uvedené hodnoty pro budoucí použití.


## <a name="uninstall"></a>Odinstalace

V současné době nepodporujeme automatizované odinstalaci FarmBeats pomocí instalačního programu. Chcete-li odebrat datové centrum nebo akcelerátor, odstraňte v Azure Portal skupinu prostředků, ve které jsou tyto součásti nainstalovány, nebo ručně odstraňte prostředky.

Pokud jste například nasadili datové centrum a akcelerátor ve dvou různých skupinách prostředků, odstraníte tyto skupiny prostředků následujícím způsobem:

1. Přihlaste se k Azure Portal.
2. V pravém horním rohu vyberte svůj účet a přepněte se na požadovaného tenanta Azure AD, kde chcete nasadit Microsoft FarmBeats.

   > [!NOTE]
   > Pro správné fungování akcelerátoru je potřeba datové centrum. Nedoporučujeme odinstalaci datového centra bez odinstalace akcelerátoru.

3. Vyberte skupiny prostředků a zadejte název skupiny prostředků akcelerátoru datového centra, kterou chcete odstranit.
4. Vyberte název skupiny prostředků. Zadáním názvu znovu spusťte kontrolu a výběrem odstranit odeberte skupinu prostředků a všechny její podkladové prostředky.
5. Případně můžete každý prostředek odstranit ručně, což se nedoporučuje.
7. Pokud chcete datový rozbočovač odstranit nebo odinstalovat, přečtěte si do skupiny prostředků přímo v Azure a odstraňte skupinu prostředků z této služby.

## <a name="next-steps"></a>Další kroky

Nasadili jste Azure FarmBeats. Nyní se naučíte, jak [vytvářet farmy](manage-farms.md#create-farms).
