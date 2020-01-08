---
title: Instalace Azure FarmBeats
description: Tento článek popisuje, jak nainstalovat Azure FarmBeats ve vašem předplatném Azure.
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482449"
---
# <a name="install-azure-farmbeats"></a>Instalace Azure FarmBeats

Tento článek popisuje, jak nainstalovat Azure FarmBeats ve svém předplatném Azure.

Azure FarmBeats je nabídka pro firmy, která je dostupná v Azure Marketplace. Umožňuje agregaci zemědělských datových sad mezi poskytovateli a generování užitečných přehledů. Azure FarmBeats to dělá tak, že vám umožní vytvořit modely umělé Intelligence (AI) nebo strojového učení (ML) založené na prostředcích s pojistnou datovou sadu. Mezi tyto dvě hlavní součásti Azure FarmBeats patří:

- **DataHub**: vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextování různých zemědělských sad dat napříč různými poskytovateli.

- **Akcelerátor**: Ukázková webová aplikace, která je postavená na DataHub. Tento odkaz – spustí vývoj a vizualizaci modelu. Akcelerátor používá rozhraní API Azure FarmBeats k předvedení vizualizace dat s příjmovým senzorem jako grafů a vizualizaci výstupu modelu jako map.

## <a name="before-you-start"></a>Než začnete
### <a name="components-installed"></a>Nainstalované součásti

Při instalaci Azure FarmBeats se ve vašem předplatném Azure zřídí tyto prostředky:

| Nainstalovaná prostředky Azure  | Komponenta Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Akcelerátor & DataHub      |
| Aplikační služba     |     Akcelerátor & DataHub     |
| Plán služby App Service   | Akcelerátor & DataHub  |
| Připojení API    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Akcelerátor & DataHub      |
| Účet Azure Batch    | Datahub   |
| Azure Key Vault |  Akcelerátor & DataHub        |
| Účet Azure Maps       |     Accelerator    |
| Obor názvů centra událostí    |     Datahub      |
| Logická aplikace      |  Datahub       |
| Účet úložiště      |     Akcelerátor & DataHub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Náklady vznikly

Náklady na Azure FarmBeats jsou agregované z nákladů na základní služby Azure. Informace o cenách služeb Azure se dají vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator). Skutečné náklady na celkovou instalaci se budou lišit v závislosti na využití. Ustálené stavové náklady na tyto dvě komponenty jsou:

* DataHub-méně než $10 za den
* Akcelerátor – méně než $2 za den

### <a name="regions-supported"></a>Podporované oblasti

V současné době se Azure FarmBeats podporuje ve veřejných cloudových prostředích v následujících oblastech:
* Austrálie – východ
* Střední USA
* Východní USA
* Východ USA 2
* Západní USA
* Západní USA 2
* Severní Evropa
* Západní Evropa
* Jihovýchodní Asie

### <a name="time-taken"></a>Doba trvání

Celá instalace Azure FarmBeats, včetně přípravy a instalace, bude trvat méně než hodinu.

## <a name="prerequisites"></a>Požadavky    

Než začnete s vlastní instalací Azure FarmBeats, musíte provést následující kroky:

### <a name="create-sentinel-account"></a>Vytvořit účet Sentinel
Vaše instalace Azure FarmBeats vám umožňuje získat zdarma satelitní dokumentaci od služby [Sentinel-2](https://scihub.copernicus.eu/) Evropské kosmické pracovní autority pro vaši farmu. Pro konfiguraci tohoto nastavení budete potřebovat účet Sentinel.

Použijte postup vytvoření bezplatného účtu s Sentinel:

1. Přejít na oficiální [registrační](https://scihub.copernicus.eu/dhus/#/self-registration) stránku.
2. Zadejte požadované podrobnosti (křestní jméno, příjmení, uživatelské jméno, heslo a ID e-mailu) a vyplňte formulář.
3. Ověřovací odkaz se pošle na registrované ID e-mailu. Vyberte odkaz uvedený v e-mailu a dokončete ověření.

Proces registrace je po dokončení ověření dokončen. Poznamenejte si **uživatelské jméno** a **heslo Sentinel**.

### <a name="decide-subscription-and-region"></a>Rozhodnutí o předplatném a oblasti

Budete potřebovat ID předplatného Azure a oblast, do které chcete nainstalovat Azure FarmBeats. Vyberte jednu z oblastí uvedených v části [podporované oblasti](#regions-supported) .

Poznamenejte si **ID předplatného Azure** a **oblast Azure**.

### <a name="verify-permissions"></a>Ověřit oprávnění

Musíte ověřit, jestli máte v tenantovi Azure dostatečná oprávnění a oprávnění pro instalaci Azure FarmBeats.

Oprávnění k přístupu můžete v Azure Portal ověřit podle pokynů v tématu [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/check-access) .

Pro instalaci Azure FarmBeats potřebujete následující oprávnění:
- Tenant – přístup pro čtení
- Předplatné – přispěvatel nebo vlastník
- Skupina prostředků – vlastník

Kromě toho Azure FarmBeats vyžaduje registraci aplikací Azure Active Directory. Existují dva způsoby, jak dokončit požadované nastavení služby Azure AD:

**Případ 1**: máte oprávnění k **zápisu** do tenanta Azure, do kterého instalujete. Tento případ znamená, že máte potřebná oprávnění k dynamickému vytvoření registrace aplikace AAD během instalace.

Můžete přejít přímo na část [dokončení registrace na webu Marketplace](#complete-azure-marketplace-sign-up) .


**Případ 2**: nemáte oprávnění k **zápisu** do tenanta Azure. Tento případ je běžný při pokusu o instalaci Azure FarmBeats do předplatného Azure vaší společnosti a přístup k **zápisu** je omezený jenom na skupinu prostředků, kterou vlastníte.
V takovém případě požádejte správce IT, aby podle následujících pokynů automaticky vygeneroval a dokončil registraci aplikace Azure AD v Azure Portal.

1. Stáhněte a extrahujte [skript generátoru aplikace AAD](https://aka.ms/FarmBeatsAADScript) do svého místního počítače.
2. Přihlaste se k Azure Portal a vyberte své předplatné a tenanta Azure AD.
3. Na panelu nástrojů v horní části Azure Portal spusťte Cloud Shell.

    ![FarmBeats projektu](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Jako preferované prostředí možností vyberte PowerShell. Při prvním zobrazení se uživateli zobrazí výzva k výběru předplatného a vytvoření účtu úložiště. Dokončete nastavení podle pokynů.
5. Nahrajte skript (z kroku 1) do Cloud Shell a poznamenejte si umístění nahraného souboru.

    > [!NOTE]
    > Ve výchozím nastavení se soubor nahraje do domovského adresáře.

6. Přejděte do domovského adresáře pomocí příkazu CD a spusťte následující skript:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Zadejte název **webu DataHub** a název **webu akcelerátoru** . Poznamenejte si výstup skriptu a sdílejte ho s osobou, která instaluje Azure FarmBeats.

Jakmile vám správce IT poskytne požadované podrobnosti, poznamenejte si **ID klienta AAD, tajný klíč klienta AAD, Datahub & název webu pro název webu akcelerátoru**.

   > [!NOTE]
   > Pokud budete postupovat podle pokynů v části případ 2, nezapomeňte přidat ID klienta AAD & tajný klíč klienta AAD jako samostatné parametry v [souboru parametrů](#prepare-parameters-file) .

Nyní máte k dispozici všechny informace, které jsou požadovány pro pokračování v další části.

### <a name="complete-azure-marketplace-sign-up"></a>Dokončení registrace Azure Marketplace

Abyste mohli spustit proces instalace na základě cloudového prostředí, musíte na Azure Marketplace dokončit registraci nabídky Azure FarmBeats. Registraci dokončíte pomocí následujících kroků:

1.  Přihlaste se k portálu Azure. V pravém horním rohu vyberte svůj účet a přepněte se na tenanta Azure AD, do kterého chcete nainstalovat Azure FarmBeats.

2.  Přejít na Azure Marketplace v rámci portálu a vyhledat **Azure FarmBeats** na webu Marketplace

3.  Zobrazí se nové okno s přehledem služby Azure FarmBeats. Vyberte **vytvořit**.

4.  Zobrazí se nové okno. Dokončete proces registrace výběrem správného předplatného, skupiny prostředků a umístění, do kterého chcete nainstalovat službu Azure FarmBeats.

5.  Po ověření zadaných podrobností vyberte **OK**. Zobrazí se stránka Podmínky použití. Přečtěte si podmínek a vyberte **vytvořit** a dokončete proces registrace.

Tento krok dokončí proces registrace v Azure Marketplace. Teď jste připraveni spustit přípravu souboru parametrů.

### <a name="prepare-parameters-file"></a>Příprava souboru parametrů
Posledním krokem fáze požadavků je vytvoření souboru JSON, který bude sloužit jako vstup během instalace Cloud Shell. Parametry v souboru JSON se musí nahradit odpovídajícími hodnotami.

Ukázkový soubor JSON je uvedený níže. Stáhněte si ukázku a aktualizujte požadované podrobnosti.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

Další informace o jednotlivých parametrech najdete v níže uvedené tabulce parametrů.

| Parametr | Hodnota|
|--- | ---|
|skj  | Poskytuje uživateli možnost nainstalovat DataHub i akcelerátor nebo jenom DataHub. Pokud chcete nainstalovat jenom DataHub, použijte "DataHub". K instalaci DataHub a akcelerátoru použijte obojí.|
|subscriptionId | Určuje předplatné Azure pro instalaci Azure FarmBeats.|
|datahubResourceGroup| Určuje název skupiny prostředků pro prostředky DataHub. Zadejte název skupiny prostředků, který jste vytvořili v Azure Marketplace sem.|
|location |Umístění/oblast Azure, do které chcete nainstalovat Azure FarmBeats|
|datahubWebsiteName  | Jedinečná předpona adresy URL webové aplikace v DataHub |
|acceleratorResourceGroup  | Určuje název skupiny prostředků pro prostředky akcelerátoru.|
|acceleratorWebsiteName |Jedinečná předpona adresy URL pro webovou aplikaci akcelerátoru|
|sentinelUsername | Uživatelské jméno pro získání ověřovacích snímků Sentinel|
|notificationEmailAddress  | E-mailová adresa pro příjem oznámení pro všechny výstrahy, které nakonfigurujete v rámci DataHub|
|updateIfExists| Volitelné Parametr, který se má zahrnout do souboru parametrů jenom v případě, že chcete upgradovat stávající instanci služby Azure FarmBeats. V případě upgradu musí být další podrobnosti, například názvy skupin prostředků a umístění, stejné.|
|aadAppClientId | Volitelné Parametr, který se má zahrnout do souboru parametrů jenom v případě, že používáte předem vytvořenou aplikaci AAD. Další podrobnosti najdete v části případ 2 v části [ověření oprávnění](#verify-permissions) . |
|aadAppClientSecret  | Volitelné Volitelné Parametr, který se má zahrnout do souboru parametrů jenom v případě, že používáte předem vytvořenou aplikaci AAD. Další podrobnosti najdete v části případ 2 v části [ověření oprávnění](#verify-permissions) .|

V závislosti na výše uvedené tabulce a ukázkovém souboru JSON vytvořte parametry souboru JSON a uložte je do místního počítače.

## <a name="install"></a>Instalace

Skutečná instalace prostředků Azure FarmBeats se stane v Cloud Shell rozhraní příkazového řádku založeném na prohlížeči pomocí prostředí bash. Pokud chcete nainstalovat Azure FarmBeats, postupujte podle následujících pokynů:

1. Přihlaste se k portálu Azure. Vyberte předplatné Azure a tenanta, pro který chcete nainstalovat Azure FarmBeats.
2. Spusťte **Cloud Shell** z panelu nástrojů v pravém horním rohu Azure Portal.
3. Jako preferované prostředí možností vyberte bash. Vyberte tlačítko **nahrát** (zvýrazněné na obrázku níže) a nahrajte soubor JSON připravených parametrů.

      ![FarmBeats projektu](./media/install-azure-farmbeats/bash-2-1.png)

4. **Zkopírujte** následující příkaz a **nahraďte \<username >** správnou hodnotou, aby příkaz odkazoval na správnou cestu nahraného souboru.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Spusťte upravený příkaz a spusťte proces instalace. Zobrazí se výzva k provedení těchto akcí:
 - Souhlasím s **licenčními podmínkami pro Azure FarmBeats** . Pokud souhlasíte s Podmínky použití, přejděte k dalšímu kroku zadáním "Y". Zadáním "N" ukončete instalaci, Pokud nesouhlasíte s podmínkami použití.

 - V dalším kroku budete vyzváni k zadání přístupového tokenu pro instalaci. Zkopírujte vygenerovaný kód a přihlaste se k přihlašovací [stránce zařízení](https://microsoft.com/devicelogin) pomocí svých **přihlašovacích údajů Azure**.

 - Po úspěšném dokončení přihlášení instalační program zobrazí výzvu k zadání hesla k účtu Sentinel. Zadejte **heslo účtu Sentinel**.

6. Soubor parametrů se ověří a spustí se instalace prostředků Azure. Dokončení instalace trvá přibližně **25 minut** .    
> [!NOTE]
> Po **20 minutách**vyprší platnost neaktivních relací Cloud Shell. Nechejte prosím aktivní relaci Cloud Shell, když instalační program nasazuje prostředky Azure. Pokud vyprší časový limit relace, budete muset proces instalace restartovat.

Po dokončení instalace se zobrazí následující odkazy na výstup:
* **DATAHUB URL**: odkaz Swagger pro přístup k rozhraním API DataHub.
* **Adresa URL akcelerátoru**: webová aplikace pro zkoumání akcelerátoru Azure FarmBeats.
* **Soubor protokolu instalačního programu**: soubor protokolu, který obsahuje podrobnosti o instalaci. Tento soubor protokolu se dá použít k řešení potíží s instalací, pokud je to potřeba.

Dokončení instalace Azure FarmBeats můžete ověřit provedením následujících kontrol:

**Datahub**
1. Přihlaste se k zadané adrese URL akcelerátoru (ve formátu **https://\<yourdatahub-website-název >. azurewebsites. NET/swaggerer**) pomocí vašich přihlašovacích údajů Azure.
2. Měli byste být schopni zobrazit různé objekty rozhraní API FarmBeats a provádět operace REST na rozhraních API.

**Accelerator**
1. Přihlaste se k zadané adrese URL akcelerátoru (ve formátu **https://\<youraccelerator-website-název >. azurewebsites. NET/swaggerer**) pomocí vašich přihlašovacích údajů Azure.
2. Měli byste být schopni zobrazit uživatelské rozhraní akcelerátoru s možností vytváření farem v prohlížeči.

Vaše schopnost provádět výše uvedené operace indikuje úspěšnou instalaci Azure FarmBeats.

## <a name="upgrade"></a>Aktualizace
Pokud chcete v Public Preview verzi upgradovat stávající instalaci Azure FarmBeats, budete muset znovu spustit instalační příkaz v Cloud Shell s parametrem další "**updateIfExists**" v souboru parametrů nastaveným na**hodnotu "true**". Přečtěte si poslední řádek níže uvedené ukázky JSON pro parametr Update.

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
Příkaz aktualizuje existující instalaci Azure FarmBeats na nejnovější verzi a poskytne vám odkazy na výstup.

## <a name="uninstall"></a>Odinstalace

Pokud chcete odinstalovat službu Azure FarmBeats DataHub nebo akcelerátor, proveďte následující kroky:

1.  Přihlaste se k Azure Portal a **odstraňte skupiny prostředků** , ve kterých jsou tyto součásti nainstalovány.

2.  Přejít na Azure Active Directory & **Odstranit aplikaci Azure AD** propojenou s instalací služby Azure FarmBeats. Tato akce odebere instalaci Azure FarmBeats z vašeho předplatného Azure.

## <a name="next-steps"></a>Další kroky
Zjistili jste, jak nainstalovat Azure FarmBeats ve svém předplatném Azure. Nyní se dozvíte, jak [Přidat uživatele](manage-users-in-azure-farmbeats.md#manage-users) do instance služby Azure FarmBeats.
