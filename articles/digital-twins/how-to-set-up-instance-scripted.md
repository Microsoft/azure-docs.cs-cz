---
title: Nastavení instance a ověřování (se skriptem)
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby digitální vlákna Azure pomocí automatizovaného skriptu nasazení.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 83741f5bc55eb222b379a274ef403f766553b21f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328629"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Nastavení instance a ověřování Azure pro digitální vlákna (skriptované)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku dokončí tento postup spuštěním [ukázky **skriptu automatizovaného nasazení** ](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/) , který zjednodušuje proces. 
* Pokud chcete zobrazit kroky ručního rozhraní příkazového řádku, které skript spouští na pozadí, přečtěte si verzi rozhraní příkazového řádku tohoto článku: [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md).
* Postup ručního provedení kroků v závislosti na Azure Portal najdete v části verze tohoto článku na portálu: [*Postupy: nastavení instance a ověřování (portál)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="prerequisites-download-the-script"></a>Požadavky: Stáhněte si skript

Vzorový skript je napsán v prostředí PowerShell. Je součástí [**ukázek digitálních vláken Azure**](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples/), které si můžete stáhnout do svého počítače tak, že přejdete na tento ukázkový odkaz a vyberete tlačítko *Stáhnout ZIP* pod nadpisem.

Tím se na váš počítač stáhne ukázkový projekt jako _**Azure_Digital_Twins_samples.zip**_. Přejděte do složky na svém počítači a rozbalte ji, abyste soubory rozbalíte.

Ve složce unzip se skript nasazení nachází v _Azure_Digital_Twins_samples > skripty > **deploy.ps1** _.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="run-the-deployment-script"></a>Spuštění zaváděcího skriptu

V tomto článku se používá ukázka kódu pro digitální vlákna Azure, která umožňuje nasadit instanci digitálních vláken Azure a požadované ověřování částečně automaticky. Můžete ji také použít jako výchozí bod pro psaní vlastních skriptových interakcí.

Tady je postup, jak spustit skript nasazení v Cloud Shell.
1. V prohlížeči přejdete do okna [Azure Cloud Shell](https://shell.azure.com/) . Přihlaste se pomocí tohoto příkazu:
    ```azurecli
    az login
    ```
    Pokud rozhraní příkazového řádku může otevřít výchozí prohlížeč, bude to mít za následek a načíst přihlašovací stránku Azure. V opačném případě otevřete stránku prohlížeče na adrese *https://aka.ms/devicelogin* a zadejte autorizační kód zobrazený v terminálu.
 
2. Na panelu s ikonami Cloud Shell se ujistěte, že je Cloud Shell nastavená tak, aby běžela verze prostředí PowerShell.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-powershell.png" alt-text="Okno Cloud Shell znázorňující výběr verze prostředí PowerShell":::

1. Vyberte ikonu Odeslat/stáhnout soubory a zvolte nahrát.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Okno Cloud Shell znázorňující výběr ikony nahrávání":::

    Přejděte na soubor _**deploy.ps1**_ na vašem počítači (v _Azure_Digital_Twins_samples > skripty > **deploy.ps1** _) a stiskněte otevřít. Tím se soubor nahraje do Cloud Shell, abyste ho mohli spustit v okně Cloud Shell.

4. Spusťte skript odesláním `./deploy.ps1` příkazu v okně Cloud Shell. (Pokud chcete vložit do Cloud Shell, můžete použít **CTRL + SHIFT + v** v systémech Windows a Linux nebo **Cmd + Shift + v** v MacOS. Můžete také použít nabídku kliknutím pravým tlačítkem myši.)

    ```azurecli
    ./deploy.ps1
    ```

    Když se skript spustí pomocí automatizovaného postupu nastavení, budete vyzváni k předání těchto hodnot:
    * Pro instanci: *ID předplatného* vašeho předplatného Azure, které se má použít.
    * Pro instanci: *umístění* , kam chcete instanci nasadit. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Pro instanci: název *skupiny prostředků* . Můžete použít existující skupinu prostředků nebo zadat nový název, který chcete vytvořit.
    * Pro instanci: *název* instance digitálního vlákna Azure. Název nové instance musí být v rámci vašeho předplatného jedinečný (to znamená, že pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zvolený název, zobrazí se výzva k výběru jiného názvu).
    * Pro registraci aplikace: *Zobrazovaný název aplikace Azure AD* , který se má přidružit k registraci. V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](how-to-use-apis-sdks.md). Později se klientská aplikace ověří proti registraci aplikace a v důsledku toho se jim přidělí nakonfigurovaná přístupová oprávnění k rozhraním API.
    * Pro registraci aplikace: *Adresa URL pro odpověď aplikace Azure AD* pro aplikaci Azure AD. Použijte `http://localhost`. Skript nastaví pro něj *veřejný klient/nativní (mobilní & Desktop)* .

Skript vytvoří instanci digitálních vláken Azure, přiřadí uživatele Azure, aby k instanci používala roli *Azure Digital Realiass Owner (Preview)* , a nastavení registrace aplikace Azure AD pro vaši klientskou aplikaci.

>[!NOTE]
>V současné době se jedná o **známý problém** s instalačním programem, ve kterém někteří uživatelé (zejména uživatelé s osobními [účty Microsoft (účty spravované služby)](https://account.microsoft.com/account)) můžou najít **přiřazení role ke _vlastníkovi digitálních vláken Azure (Preview)_ se nevytvořilo**.
>
>Přiřazení role můžete ověřit v části [*přiřazení role uživatele*](#verify-user-role-assignment) dále v tomto článku, a pokud je to potřeba, nastavte přiřazení role ručně pomocí [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) nebo [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Další podrobnosti o tomto problému najdete v tématu [*řešení potíží: známé problémy v Azure Digital autovlákna*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

Tady je ukázka výstupního protokolu ze skriptu:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Okno Cloud Shell zobrazující protokol vstupů a výstupů prostřednictvím spuštění skriptu nasazení" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Pokud se skript úspěšně dokončí, vysloví se konečný výtisk `Deployment completed successfully` . Jinak vyřešte chybovou zprávu a znovu spusťte skript. Přeskočí kroky, které jste už dokončili, a začněte znovu požádat o vstup v místě, kde jste skončili.

Po dokončení skriptu teď máte k dispozici instanci digitálních vláken Azure, která je připravená k tomu, aby se mohla spravovat, a měli byste nastavit oprávnění pro klientské aplikace pro přístup k ní.

> [!NOTE]
> Skript aktuálně přiřadí požadovanou roli správy v rámci digitálních vláken Azure (ve*verzi Preview)* pro stejného uživatele, který spouští skript z Cloud Shell. Pokud potřebujete tuto roli přiřadit někomu jinému, kdo bude spravovat instanci, můžete to udělat hned pomocí Azure Portal ([pokyny](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) nebo rozhraní příkazového řádku ([pokynů](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

## <a name="collect-important-values"></a>Shromažďovat důležité hodnoty

Existuje několik důležitých hodnot z prostředků nastavených skriptem, které můžete potřebovat při práci s instancí digitálních vláken Azure. V této části použijete [Azure Portal](https://portal.azure.com) ke shromáždění těchto hodnot. Měli byste je uložit na bezpečné místo, nebo se vrátit do této části, abyste je později našli, až je budete potřebovat.

Pokud budou jiné uživatele programovat s instancí, měli byste je také nasdílet pomocí těchto hodnot.

### <a name="collect-instance-values"></a>Shromáždit hodnoty instance

V [Azure Portal](https://portal.azure.com)Najděte instanci digitálních vláken Azure pomocí hledání názvu vaší instance na panelu hledání na portálu.

Výběrem této možnosti se otevře stránka *Přehled* instance. Poznamenejte si jeho *název*, *skupinu prostředků*a *název hostitele*. Později je budete možná potřebovat k identifikaci a připojení k instanci.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Zvýrazňování důležitých hodnot na stránce přehledu instance":::

### <a name="collect-app-registration-values"></a>Shromažďovat hodnoty registrace aplikace 

Existují dvě důležité hodnoty pro registraci aplikace, které budete potřebovat později k [zápisu ověřovacího kódu klientské aplikace pro rozhraní API digitálních vláken Azure](how-to-authenticate-client.md). 

Pokud je chcete najít, přejděte pomocí [tohoto odkazu](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) na stránku s přehledem registrace aplikace Azure AD v Azure Portal. Tato stránka zobrazuje všechny registrace aplikací, které byly vytvořeny v rámci vašeho předplatného.

Měla by se zobrazit registrace aplikace, kterou jste právě vytvořili v tomto seznamu. Vyberte ho a otevřete jeho podrobnosti:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Zobrazení důležitých hodnot pro registraci aplikace na portálu":::

Poznamenejte si *ID aplikace (klienta)* a *ID adresáře (tenanta)* **zobrazené na stránce** . Pokud nejste osoba, která bude psát kód pro klientské aplikace, budete muset tyto hodnoty sdílet s osobou, která bude.

## <a name="verify-success"></a>Ověřit úspěch

Pokud chcete ověřit vytvoření prostředků a oprávnění nastavených skriptem, můžete si je prohlédnout v [Azure Portal](https://portal.azure.com).

Pokud nemůžete ověřit úspěch libovolného kroku, zkuste krok zopakovat. Jednotlivé kroky můžete provádět podle pokynů [Azure Portal](how-to-set-up-instance-portal.md) nebo [CLI](how-to-set-up-instance-cli.md) .

### <a name="verify-instance"></a>Ověřit instanci

Pokud chcete ověřit, jestli se vaše instance vytvořila, na [stránce Azure Portal digitální vlákna](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) na webu Azure. Na tuto stránku se dostanete tak, že na panelu hledání na portálu vyhledáte *digitální vlákna Azure* .

Tato stránka obsahuje seznam všech instancí digitálních vláken Azure. V seznamu vyhledejte název nově vytvořené instance.

Pokud ověření nebylo úspěšné, můžete znovu vytvořit instanci pomocí [portálu](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) nebo rozhraní příkazového [řádku](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="verify-user-role-assignment"></a>Ověřit přiřazení role uživatele

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Odvolání, že skript aktuálně přiřadí tuto požadovanou roli stejnému uživateli, který spouští skript z Cloud Shell. Pokud potřebujete tuto roli přiřadit někomu jinému, kdo bude spravovat instanci, můžete to udělat hned pomocí Azure Portal ([pokyny](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) nebo rozhraní příkazového řádku ([pokynů](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Pokud ověření nebylo úspěšné, můžete také znovu použít vlastní přiřazení role pomocí [portálu](how-to-set-up-instance-portal.md#set-up-user-access-permissions) nebo rozhraní příkazového [řádku](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

### <a name="verify-app-registration"></a>Ověřit registraci aplikace

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Dále ověřte, že nastavení oprávnění digitálního vlákna Azure byla správně nastavena při registraci. Provedete to tak, že v řádku nabídek vyberete *manifest* a zobrazí se kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte tato pole v části `requiredResourceAccess` . Hodnoty by měly odpovídat hodnotám na snímku obrazovky níže:

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

Pokud jeden nebo oba tyto kroky ověřování neproběhlo úspěšně, zkuste registraci aplikace znovu vytvořit pomocí [portálu](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) nebo pokynů pro rozhraní příkazového [řádku](how-to-set-up-instance-cli.md#set-up-access-permissions-for-client-applications) .

## <a name="other-possible-steps-for-your-organization"></a>Další možné kroky pro vaši organizaci

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci, napsáním ověřovacího kódu klientské aplikace:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)
