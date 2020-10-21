---
title: Instalace služby Azure FarmBeats
description: Tento článek popisuje, jak nainstalovat Azure FarmBeats ve vašem předplatném Azure.
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 2535c05241c076e08f8f0f2ba9e2301fb353723e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330474"
---
# <a name="install-azure-farmbeats"></a>Instalace služby Azure FarmBeats

Tento článek popisuje, jak nainstalovat Azure FarmBeats ve svém předplatném Azure.

Azure FarmBeats je nabídka pro firmy, která je dostupná v Azure Marketplace. Umožňuje agregaci zemědělských datových sad mezi poskytovateli a generování užitečných přehledů. Azure FarmBeats to dělá tak, že vám umožní vytvořit modely umělé Intelligence (AI) nebo strojového učení (ML) založené na prostředcích s pojistnou datovou sadu. Mezi tyto dvě hlavní součásti Azure FarmBeats patří:

- **DataHub**: vrstva rozhraní API, která umožňuje agregaci, normalizaci a kontextování různých zemědělských sad dat napříč různými poskytovateli.

- **Akcelerátor**: webová aplikace, která je postavená na DataHub. Tento odkaz – spustí vývoj a vizualizaci modelu. Akcelerátor používá rozhraní API Azure FarmBeats k předvedení vizualizace dat s příjmovým senzorem jako grafů a vizualizaci výstupu modelu jako map.

## <a name="general-information"></a>Obecné informace

### <a name="components-installed"></a>Nainstalované součásti

Při instalaci Azure FarmBeats se ve vašem předplatném Azure zřídí tyto prostředky:

| Nainstalovaná prostředky Azure  | Komponenta Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Akcelerátor & DataHub      |
| App Service     |     Akcelerátor & DataHub     |
| Plán služby App Service   | Akcelerátor & DataHub  |
| Připojení rozhraní API    |  Datahub       |
| Azure Cache for Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Akcelerátor & DataHub      |
| Účet Azure Batch    | Datahub   |
| Azure Key Vault |  Akcelerátor & DataHub        |
| Účet Azure Maps       |     Accelerator    |
| Obor názvů centra událostí    |     Datahub      |
| Aplikace logiky      |  Datahub       |
| Storage Account (Účet úložiště)      |     Akcelerátor & DataHub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Náklady vznikly

Náklady na Azure FarmBeats jsou agregované z nákladů na základní služby Azure. Informace o cenách služeb Azure se dají vypočítat pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator). Skutečné náklady na celkovou instalaci se budou lišit v závislosti na využití. Ustálené stavové náklady na tyto dvě komponenty jsou:

- DataHub-méně než $10 za den
- Akcelerátor – méně než $2 za den

### <a name="regions-supported"></a>Podporované oblasti

V současné době se Azure FarmBeats podporuje ve veřejných cloudových prostředích v následujících oblastech:

- Austrálie – východ
- Střední USA
- East US
- USA – východ 2
- USA – západ
- Západní USA 2
- Severní Evropa
- West Europe
- Východní Asie
- Jihovýchodní Asie

### <a name="time-taken"></a>Doba trvání

Celá instalace Azure FarmBeats, včetně přípravy a instalace, bude trvat méně než hodinu.

## <a name="prerequisites"></a>Předpoklady

Než začnete s vlastní instalací Azure FarmBeats, musíte provést následující kroky:

### <a name="verify-permissions"></a>Ověřit oprávnění

V tenantovi Azure budete potřebovat následující oprávnění pro instalaci Azure FarmBeats:

- Tenant – Autor aplikace AAD
- Předplatné – vlastník
- Skupina prostředků, ve které se FarmBeats instaluje – vlastník

První dvě oprávnění jsou potřebná k [Vytvoření kroku aplikace AAD](#create-an-aad-application) . V případě potřeby můžete získat někoho s příslušnými oprávněními k vytvoření aplikace AAD.

Osoba, která spouští FarmBeats Install z Marketplace, musí být vlastníkem skupiny prostředků, ve které se instaluje FarmBeats. Pro vlastníky předplatného se k tomu automaticky dojde při vytvoření skupiny prostředků. Pro jiné nastavte skupinu prostředků předem a požádejte vlastníka předplatného, aby vás vytvořil jako vlastník skupiny prostředků.

Oprávnění k přístupu můžete v Azure Portal ověřit podle pokynů v tématu [řízení přístupu na základě role v Azure](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Rozhodnutí o předplatném a oblasti

Budete potřebovat ID předplatného Azure a oblast, do které chcete nainstalovat Azure FarmBeats. Vyberte jednu z oblastí uvedených v části [podporované oblasti](#regions-supported) .

Poznamenejte si **ID předplatného Azure** a **oblast Azure**.

### <a name="create-an-aad-application"></a>Vytvoření aplikace AAD

Azure FarmBeats vyžaduje vytvoření a registraci aplikace Azure Active Directory. K úspěšnému spuštění skriptu pro vytvoření AAD jsou potřeba následující oprávnění:

- Tenant – Autor aplikace AAD
- Předplatné – vlastník

Spusťte následující postup v instanci Cloud Shell pomocí prostředí PowerShell. Uživatelé budou při prvním spuštění vyzváni k výběru předplatného a vytvoření účtu úložiště. Dokončete nastavení podle pokynů.

1. Stáhnout [skript generátoru aplikace AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Ve výchozím nastavení se soubor stáhne do domovského adresáře. Přejděte do adresáře.

    ```azurepowershell-interactive
        cd
    ```

3. Spuštění skriptu AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Skript si vyžádá následující tři vstupy:

    - **FarmBeats název webu**: Jedná se o jedinečnou PŘEDPONU adresy URL vaší webové aplikace FarmBeats. V případě, že předpona už je obsazená, skript se zobrazí jako chyba. Po instalaci bude vaše nasazení FarmBeats dostupné z https:// \<FarmBeats-website-name> . azurewebsites.NET a rozhraní API Swagger budou v https:// \<FarmBeats-website-name> -API.azurewebsites.NET.

    - **Přihlašovací ID Azure**: zadejte přihlašovací ID Azure pro uživatele, kterého chcete přidat jako správce FarmBeats. Tento uživatel pak může udělit přístup pro přístup k webové aplikaci FarmBeats jiným uživatelům. Přihlašovací ID je všeobecně ve formátu john.doe@domain.com . Podporuje se i hlavní název uživatele (UPN) Azure.

    - **ID předplatného**: Toto je ID předplatného, ve kterém chcete nainstalovat službu Azure FarmBeats.

5. Spuštění skriptu AAD trvá přibližně 2 minuty a na obrazovce se vytvoří výstup hodnot na obrazovce a také soubor JSON ve stejném adresáři. Pokud jste museli skript spustit někomu jinému, požádejte ho, aby s vámi nasdílel tento výstup.

### <a name="create-sentinel-account"></a>Vytvořit účet Sentinel

Vaše instalace Azure FarmBeats vám umožní získat satelitní dokumentaci z oblasti satelitu [Sentinel-2](https://scihub.copernicus.eu/) Evropské kosmické organizace pro vaši farmu. Pro konfiguraci tohoto nastavení budete potřebovat účet Sentinel.

Použijte postup vytvoření bezplatného účtu s Sentinel:

1. Přejít na oficiální [registrační](https://aka.ms/SentinelRegistration) stránku.
2. Zadejte požadované podrobnosti (křestní jméno, příjmení, uživatelské jméno, heslo a ID e-mailu) a vyplňte formulář.
3. Ověřovací odkaz se pošle na registrované ID e-mailu. Vyberte odkaz uvedený v e-mailu a dokončete ověření.

Proces registrace je dokončený. Až se ověření dokončí, poznamenejte si **uživatelské jméno** a **heslo Sentinel**.

## <a name="install"></a>Instalace

Nyní jste připraveni nainstalovat FarmBeats. Spusťte instalaci podle následujících kroků:

1. Přihlaste se k portálu Azure. V pravém horním rohu vyberte svůj účet a přepněte se na tenanta Azure AD, do kterého chcete nainstalovat Azure FarmBeats.

2. Na portálu můžete přejít na Azure Marketplace a vyhledat **Azure FarmBeats** na webu Marketplace.

3. Zobrazí se nové okno s přehledem služby Azure FarmBeats. Vyberte **Vytvořit**.

4. Zobrazí se nové okno. Dokončete proces registrace výběrem správného předplatného, skupiny prostředků a umístění, do kterého chcete nainstalovat službu Azure FarmBeats.

5. Zadejte e-mailovou adresu, která by měla přijímat výstrahy služby související se službou Azure FarmBeats v části **výstrahy služby FarmBeats** . V dolní části stránky vyberte **Další** a přejděte na kartu **závislosti** .

    ![Karta základy](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Zkopírujte jednotlivé položky z výstupu [skriptu AAD](#create-an-aad-application) do vstupů v části aplikace AAD.

7. V části účet Sentinel zadejte uživatelské jméno a heslo [účtu Sentinel](#create-sentinel-account) . Kliknutím na tlačítko **Další** přejdete na kartu **Revize + vytvořit** .

    ![Karta závislosti](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Po ověření zadaných podrobností vyberte **OK**. Zobrazí se stránka Podmínky použití. Přečtěte si podmínek a vyberte **vytvořit** a spusťte instalaci. Budete přesměrováni na stránku, kde můžete postupovat podle pokynů k instalaci.

Po dokončení instalace můžete ověřit instalaci a začít používat portál FarmBeats, a to tak, že přejdete na název webu, který jste zadali během instalace: https:// \<FarmBeats-website-name> . azurewebsites.NET. Mělo by se zobrazit uživatelské rozhraní FarmBeats s možností vytváření farem.

**DataHub** najdete na adrese https:// \<FarmBeats-website-name> -API.azurewebsites.NET/Swagger. Tady uvidíte různé objekty rozhraní API FarmBeats a na rozhraních API se provádějí operace REST.

## <a name="upgrade"></a>Upgrade

Pokud chcete upgradovat FarmBeats na nejnovější verzi, spusťte následující kroky v instanci Cloud Shell pomocí prostředí PowerShell. Uživatel bude muset být vlastníkem předplatného, ve kterém je FarmBeats nainstalovaný.

Uživatelé budou při prvním spuštění vyzváni k výběru předplatného a vytvoření účtu úložiště. Dokončete nastavení podle pokynů.

1. Stažení [skriptu pro upgrade](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Ve výchozím nastavení se soubor stáhne do domovského adresáře. Přejděte do adresáře.

    ```azurepowershell-interactive
        cd
    ```

3. Spuštění skriptu upgradu

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Cesta k input.jssouboru je volitelná. Pokud není zadaný, skript se vyzve pro všechny požadované vstupy. Upgrade by se měl dokončit během přibližně 30 minut.

## <a name="uninstall"></a>Odinstalace

Pokud chcete odinstalovat službu Azure FarmBeats DataHub nebo akcelerátor, proveďte následující kroky:

1. Přihlaste se k Azure Portal a **odstraňte skupiny prostředků** , ve kterých jsou tyto součásti nainstalovány.

2. Přejít na Azure Active Directory & **Odstranit aplikaci Azure AD** propojenou s instalací služby Azure FarmBeats.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nainstalovat Azure FarmBeats ve svém předplatném Azure. Nyní se dozvíte, jak [Přidat uživatele](manage-users-in-azure-farmbeats.md#manage-users) do instance služby Azure FarmBeats.
