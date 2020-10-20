---
title: Nastavení instance a ověřování (se skriptem)
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby digitální vlákna Azure pomocí automatizovaného skriptu nasazení.
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 10d4d07a61bc4ebec789d53e4271a3bcdc7ba76b
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205540"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-scripted"></a>Nastavení instance a ověřování Azure pro digitální vlákna (skriptované)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku dokončí tento postup spuštěním [ukázky **skriptu automatizovaného nasazení** ](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) , který zjednodušuje proces. 
* Pokud chcete zobrazit kroky ručního rozhraní příkazového řádku, které skript spouští na pozadí, přečtěte si verzi rozhraní příkazového řádku tohoto článku: [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md).
* Postup ručního provedení kroků v závislosti na Azure Portal najdete v části verze tohoto článku na portálu: [*Postupy: nastavení instance a ověřování (portál)*](how-to-set-up-instance-portal.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="prerequisites-download-the-script"></a>Požadavky: Stáhněte si skript

Vzorový skript je napsán v prostředí PowerShell. Je součástí [**komplexních ukázek Azure pro digitální vlákna**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/), které si můžete stáhnout do svého počítače tak, že přejdete na tento ukázkový odkaz a vyberete tlačítko *Stáhnout ZIP* pod nadpisem.

Tím se na váš počítač stáhne ukázkový projekt jako _**Azure_Digital_Twins_end_to_end_samples.zip**_. Přejděte do složky na svém počítači a rozbalte ji, abyste soubory rozbalíte.

Ve složce unzip se skript nasazení nachází v _Azure_Digital_Twins_end_to_end_samples > skripty > **deploy.ps1** _.

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

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Okno Cloud Shell znázorňující výběr verze prostředí PowerShell":::

    Přejděte na soubor _**deploy.ps1**_ na vašem počítači (v _Azure_Digital_Twins_end_to_end_samples > skripty > **deploy.ps1** _) a stiskněte otevřít. Tím se soubor nahraje do Cloud Shell, abyste ho mohli spustit v okně Cloud Shell.

4. Spusťte skript odesláním `./deploy.ps1` příkazu v okně Cloud Shell. Níže uvedený příkaz můžete zkopírovat (pokud ho chcete vložit do Cloud Shell, můžete použít **CTRL + SHIFT + v** v systémech Windows a Linux nebo **Cmd + Shift + v** v MacOS. Můžete také použít nabídku klikněte pravým tlačítkem myši.

    ```azurecli
    ./deploy.ps1
    ```

    Skript vytvoří instanci digitálních vláken Azure a přiřadí uživatele Azure, který má na instanci roli *vlastníka digitálních vláken Azure (Preview)* .

    Když se skript spustí pomocí automatizovaného postupu nastavení, budete vyzváni k předání těchto hodnot:
    * Pro instanci: *ID předplatného* vašeho předplatného Azure, které se má použít.
    * Pro instanci: *umístění* , kam chcete instanci nasadit. Pokud chcete zjistit, které oblasti podporují digitální vlákna Azure, přejděte na [*produkty Azure dostupné v jednotlivých oblastech*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
    * Pro instanci: název *skupiny prostředků* . Můžete použít existující skupinu prostředků nebo zadat nový název, který chcete vytvořit.
    * Pro instanci: *název* instance digitálního vlákna Azure. Název nové instance musí být v rámci vašeho předplatného jedinečný (to znamená, že pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zvolený název, zobrazí se výzva k výběru jiného názvu).

Tady je ukázka výstupního protokolu ze skriptu:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png" alt-text="Okno Cloud Shell znázorňující výběr verze prostředí PowerShell" lightbox="media/how-to-set-up-instance/cloud-shell/deployment-script-output.png":::

Pokud se skript úspěšně dokončí, vysloví se konečný výtisk `Deployment completed successfully` . Jinak vyřešte chybovou zprávu a znovu spusťte skript. Přeskočí kroky, které jste už dokončili, a začněte znovu požádat o vstup v místě, kde jste skončili.

> [!NOTE]
> Skript aktuálně přiřadí požadovanou roli správy v rámci digitálních vláken Azure (ve*verzi Preview)* pro stejného uživatele, který spouští skript z Cloud Shell. Pokud potřebujete tuto roli přiřadit někomu jinému, kdo bude spravovat instanci, můžete to udělat hned pomocí Azure Portal ([pokyny](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) nebo rozhraní příkazového řádku ([pokynů](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

>[!NOTE]
>V současné době se jedná o **známý problém** s instalačním programem, ve kterém někteří uživatelé (zejména uživatelé s osobními [účty Microsoft (účty spravované služby)](https://account.microsoft.com/account)) můžou najít **přiřazení role ke _vlastníkovi digitálních vláken Azure (Preview)_ se nevytvořilo**.
>
>Přiřazení role můžete ověřit v části [*přiřazení role uživatele*](#verify-user-role-assignment) dále v tomto článku, a pokud je to potřeba, nastavte přiřazení role ručně pomocí [Azure Portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions) nebo [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions).
>
>Další podrobnosti o tomto problému najdete v tématu [*řešení potíží: známé problémy v Azure Digital autovlákna*](troubleshoot-known-issues.md#missing-role-assignment-after-scripted-setup).

## <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Pokud chcete ověřit vytvoření prostředků a oprávnění nastavených skriptem, můžete se na ně podívat v [Azure Portal](https://portal.azure.com) s pokyny uvedenými níže. Pokud nemůžete ověřit úspěch libovolného kroku, zkuste krok zopakovat. Jednotlivé kroky můžete provádět podle pokynů [Azure Portal](how-to-set-up-instance-portal.md) nebo [CLI](how-to-set-up-instance-cli.md) .

V této části najdete taky informace o tom, jak najít důležité hodnoty z prostředků nastavených skriptem, který můžete potřebovat při práci s instancí digitálních vláken Azure. Tyto hodnoty byste měli uložit na bezpečném místě, nebo se můžete vrátit do této části, abyste je později našli, až je budete potřebovat. Pokud budou jiné uživatele programovat s instancí, měli byste tyto hodnoty také sdílet.

### <a name="verify-instance"></a>Ověřit instanci

Pokud chcete ověřit, jestli se vaše instance vytvořila, na [stránce Azure Portal digitální vlákna](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) na webu Azure. Na tuto stránku se dostanete tak, že na panelu hledání na portálu vyhledáte *digitální vlákna Azure* .

Tato stránka obsahuje seznam všech instancí digitálních vláken Azure. V seznamu vyhledejte název nově vytvořené instance.

Pokud ověření nebylo úspěšné, můžete znovu vytvořit instanci pomocí [portálu](how-to-set-up-instance-portal.md#create-the-azure-digital-twins-instance) nebo rozhraní příkazového [řádku](how-to-set-up-instance-cli.md#create-the-azure-digital-twins-instance).

### <a name="collect-instance-values"></a>Shromáždit hodnoty instance

Vyberte název vaší instance ze [stránky digitálních vláken Azure](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) a otevřete stránku *Přehled* instance. Poznamenejte si jeho *název*, *skupinu prostředků*a *název hostitele*. Později je budete možná potřebovat k identifikaci a připojení k instanci.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Okno Cloud Shell znázorňující výběr verze prostředí PowerShell":::

### <a name="verify-user-role-assignment"></a>Ověřit přiřazení role uživatele

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

> [!NOTE]
> Odvolání, že skript aktuálně přiřadí tuto požadovanou roli stejnému uživateli, který spouští skript z Cloud Shell. Pokud potřebujete tuto roli přiřadit někomu jinému, kdo bude spravovat instanci, můžete to udělat hned pomocí Azure Portal ([pokyny](how-to-set-up-instance-portal.md#set-up-user-access-permissions)) nebo rozhraní příkazového řádku ([pokynů](how-to-set-up-instance-cli.md#set-up-user-access-permissions)).

Pokud ověření nebylo úspěšné, můžete také znovu použít vlastní přiřazení role pomocí [portálu](how-to-set-up-instance-portal.md#set-up-user-access-permissions) nebo rozhraní příkazového [řádku](how-to-set-up-instance-cli.md#set-up-user-access-permissions).

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci s ověřovacím kódem:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)