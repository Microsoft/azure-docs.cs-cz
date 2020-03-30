---
title: Instalace služby Azure FarmBeats
description: Tento článek popisuje, jak nainstalovat Azure FarmBeats ve vašem předplatném Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479555"
---
# <a name="install-azure-farmbeats"></a>Instalace služby Azure FarmBeats

Tento článek popisuje, jak nainstalovat Azure FarmBeats ve vašem předplatném Azure.

Azure FarmBeats je nabídka mezi podniky, která je dostupná na Azure Marketplace. Umožňuje agregaci zemědělských datových souborů mezi poskytovateli a generování užitečných poznatků. Azure FarmBeats to umožňuje vytvářet modely umělé inteligence (AI) nebo strojového učení (ML) na základě tavené datové sady. Dvě hlavní součásti Azure FarmBeats jsou:

- **Datahub**: Vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextovou analýzu různých datových sad zemědělství napříč různými poskytovateli.

- **Akcelerátor**: Webová aplikace, která je postavena na Datahubu. To skok-spustí vývoj modelu a vizualizaci. Akcelerátor používá Azure FarmBeats ROZHRANÍ API k předvedení vizualizace ingestovaných dat senzorů jako grafy a vizualizace výstupu modelu jako mapy.

## <a name="general-information"></a>Obecné informace

### <a name="components-installed"></a>Instalované součásti

Při instalaci Azure FarmBeats se ve vašem předplatném Azure zřídí následující prostředky:

| Nainstalované prostředky Azure  | Součást Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Datový & akcelerátor      |
| App Service     |     Datový & akcelerátor     |
| Plán služby App Service   | Datový & akcelerátor  |
| Připojení rozhraní API    |  Datový hub       |
| Azure Cache for Redis       | Datový hub      |
| Azure Cosmos DB   |  Datový hub       |
| Azure Data Factory V2       |     Datový & akcelerátor      |
| Účet Azure Batch    | Datový hub   |
| Azure Key Vault |  Datový & akcelerátor        |
| Účet Azure Maps       |     Accelerator    |
| Obor názvů centra událostí    |     Datový hub      |
| Aplikace logiky      |  Datový hub       |
| Účet úložiště      |     Datový & akcelerátor      |
| Time Series Insights     |    Datový hub    |

### <a name="costs-incurred"></a>Vzniklé náklady

Náklady na Azure FarmBeats je souhrn nákladů na základní služby Azure. Informace o cenách pro služby Azure lze vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator). Skutečné náklady na celkovou instalaci se budou lišit v závislosti na použití. Náklady na ustálený stav pro dvě složky jsou:

- Datahub – méně než 10 USD za den
- Akcelerátor - méně než $2 za den

### <a name="regions-supported"></a>Podporované regiony

V současné době azure farmbeats je podporována v prostředí veřejného cloudu v následujících oblastech:

- Austrálie – východ
- USA – střed
- USA – východ
- USA – východ 2
- USA – západ
- USA – západ 2
- Severní Evropa
- Západní Evropa
- Východní Asie
- Jihovýchodní Asie

### <a name="time-taken"></a>Doba doby

Celé nastavení Azure FarmBeats, včetně přípravy a instalace bude trvat méně než hodinu.

## <a name="prerequisites"></a>Požadavky

Před zahájením vlastní instalace Azure FarmBeats budete muset provést následující kroky:

### <a name="verify-permissions"></a>Ověřit oprávnění

K instalaci Azure FarmBeats budete potřebovat následující oprávnění v tenantovi Azure:

- Tenant – tvůrce aplikace AAD
- Předplatné – vlastník
- Skupina prostředků, ve které je farmbeats instalována – vlastník

První dvě oprávnění jsou potřebné pro vytvoření kroku [aplikace AAD.](#create-an-aad-application) V případě potřeby můžete získat někoho s příslušnými oprávněními k vytvoření aplikace AAD.

Osoba, která provozuje instalaci FarmBeats z trhu, musí být vlastníkem skupiny zdrojů, ve které je farmbeats instalován. Pro vlastníky předplatného k tomu dojde automaticky při vytvoření skupiny prostředků. Pro ostatní, prosím, pre-vytvořit skupinu prostředků a požádejte vlastníka předplatného, aby vás vlastník skupiny prostředků.

Svá přístupová oprávnění na webu Azure Portal můžete ověřit podle pokynů k [řízení přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Rozhodněte předplatné a oblast

Budete potřebovat ID předplatného Azure a oblast, do které chcete nainstalovat Azure FarmBeats. Zvolte jednu z oblastí uvedených v části [Podporované oblasti.](#regions-supported)

Poznamenejte si **ID předplatného Azure** a **oblast Azure**.

### <a name="create-an-aad-application"></a>Vytvoření aplikace AAD

Azure FarmBeats vyžadují vytvoření a registraci aplikací Azure Active Directory. Chcete-li úspěšně spustit skript pro vytvoření služby AAD, jsou zapotřebí následující oprávnění:

- Tenant – tvůrce aplikace AAD
- Předplatné – vlastník

Spusťte následující kroky v instanci cloudového prostředí pomocí prostředí PowerShell. První uživatelé budou vyzváni k výběru předplatného a vytvoření účtu úložiště. Dokončete nastavení podle pokynů.

1. Stáhněte si [skript generátoru aplikací AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Ve výchozím nastavení je soubor stažen do domovského adresáře. Přejděte do adresáře.

    ```azurepowershell-interactive
        cd
    ```

3. Spuštění skriptu AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skript požádá o následující tři vstupy:

    - **FarmBeats název webových stránek:** Toto je unikátní URL předpona pro vaši webovou aplikaci FarmBeats. V případě, že je předpona již přijata, skript se změní. Po instalaci bude vaše nasazení FarmBeats\<přístupné z https:// FarmBeats-website-name>.azurewebsites.net a\<rozhraní API bude na https:// FarmBeats-website-name>-api.azurewebsites.net

    - **Azure login ID**: Zadejte ID přihlášení Azure pro uživatele, který chcete přidat jako správce FarmBeats. Tento uživatel pak může udělit přístup k webové aplikaci FarmBeats ostatním uživatelům. ID přihlášení je obecně john.doe@domain.comformuláře . Azure UPN je také podporována.

    - **ID předplatného:** Toto je ID předplatného, ve kterém chcete nainstalovat Azure FarmBeats

5. Spuštění skriptu AAD trvá přibližně 2 minuty a výstupy hodnot na obrazovce, stejně jako do souboru json ve stejném adresáři. Pokud máte někoho jiného spustit skript, požádejte ho, aby sdílet tento výstup s vámi.

### <a name="create-sentinel-account"></a>Vytvořit účet Sentinel

Vaše nastavení Azure FarmBeats vám umožní získat satelitní snímky ze satelitní mise [Sentinel-2](https://scihub.copernicus.eu/) Evropské kosmické agentury pro vaši farmu. Chcete-li nakonfigurovat toto nastavení, budete potřebovat účet Sentinel.

Postupujte podle pokynů k vytvoření bezplatného účtu u Sentinelu:

1. Přejděte na oficiální [registrační](https://aka.ms/SentinelRegistration) stránku.
2. Zadejte požadované údaje (jméno, příjmení, uživatelské jméno, heslo a ID e-mailu) a vyplňte formulář.
3. Na registrované ID e-mailu je odeslán ověřovací odkaz. Vyberte odkaz uvedený v e-mailu a dokončete ověření.

Proces registrace je dokončen. Poznamenejte si své **sentinelové uživatelské jméno** a **sentinelové heslo**, jakmile je ověření také dokončeno.

## <a name="install"></a>Instalace

Nyní jste připraveni k instalaci FarmBeats. Chcete-li zahájit instalaci, postupujte podle následujících kroků:

1. Přihlaste se k portálu Azure. Vyberte svůj účet v pravém horním rohu a přepněte na klienta Azure AD, kde chcete nainstalovat Azure FarmBeats.

2. Přejděte na Azure Marketplace na portálu a vyhledejte **Azure FarmBeats** na Marketplace.

3. Zobrazí se nové okno s přehledem Azure FarmBeats. Vyberte **Vytvořit**.

4. Zobrazí se nové okno. Proces registrace dokončete výběrem správného předplatného, skupiny prostředků a umístění, do kterého chcete nainstalovat Azure FarmBeats.

5. Zadejte e-mailovou adresu, která by měla přijímat všechny výstrahy služby související s Azure FarmBeats v části **Upozornění služby FarmBeats.** Vyberte **Další** v dolní části stránky, chcete-li přejít na kartu **Závislosti.**

    ![Karta Základy](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Zkopírujte jednotlivé položky z výstupu [skriptu AAD](#create-an-aad-application) do vstupů v části aplikace AAD.

7. Do části Účet Sentinel zadejte uživatelské jméno a heslo [účtu Sentinel.](#create-sentinel-account) Chcete-li přejít na kartu **Revize + Vytvořit,** vyberte **Možnost Další.**

    ![Karta Závislosti](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Po ověření zadaných údajů vyberte **možnost OK**. Zobrazí se stránka Podmínky použití. Zkontrolujte podmínky a vyberte **Vytvořit** pro spuštění instalace. Budete přesměrováni na stránku, kde můžete sledovat průběh instalace.

Po dokončení instalace můžete ověřit instalaci a začít používat portál FarmBeats tak, že přejdete\<na název webové stránky, který jste zadali během instalace: https:// FarmBeats-website-name>.azurewebsites.net. Měli byste vidět FarmBeats uživatelské rozhraní s možností vytvořit farmy.

**Datahub** lze nalézt\<na adrese https:// FarmBeats-website-name>-api.azurewebsites.net/swagger. Zde uvidíte různé objekty rozhraní API FarmBeats a provádět operace REST na rozhraní API.

## <a name="upgrade"></a>Upgrade

Chcete-li upgradovat FarmBeats na nejnovější verzi, spusťte následující kroky v instanci prostředí Cloud Shell pomocí prostředí PowerShell. Uživatel bude muset být vlastníkem předplatného, ve kterém je nainstalován FarmBeats.

První uživatelé budou vyzváni k výběru předplatného a vytvoření účtu úložiště. Dokončete nastavení podle pokynů.

1. Stažení [skriptu pro upgrade](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Ve výchozím nastavení je soubor stažen do domovského adresáře. Přejděte do adresáře.

    ```azurepowershell-interactive
        cd
    ```

3. Spuštění skriptu pro upgrade

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Cesta k souboru input.json je volitelná. Pokud není zadán, skript požádá o všechny požadované vstupy. Upgrade by měl skončit přibližně za 30 minut.

## <a name="uninstall"></a>Odinstalace

Chcete-li odinstalovat Azure FarmBeats Datahub nebo Akcelerátor, proveďte následující kroky:

1. Přihlaste se k portálu Azure a **odstraňte skupiny prostředků,** ve kterých jsou tyto součásti nainstalovány.

2. Přejděte na Azure Active Directory & **odstranit aplikaci Azure AD** propojenou s instalací Azure FarmBeats.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nainstalovat Azure FarmBeats ve vašem předplatném Azure. Teď se dozvíte, jak [přidat uživatele](manage-users-in-azure-farmbeats.md#manage-users) do instance Azure FarmBeats.
