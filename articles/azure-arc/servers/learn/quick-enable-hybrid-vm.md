---
title: Připojit hybridní počítač k serverům s podporou ARC Azure
description: Naučte se připojit a zaregistrovat svůj hybridní počítač se servery s podporou ARC Azure.
ms.topic: quickstart
ms.date: 12/15/2020
ms.openlocfilehash: 68869854cbfcf6d7297137e6239b2229a20c04a1
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516779"
---
# <a name="quickstart-connect-hybrid-machine-with-azure-arc-enabled-servers"></a>Rychlý Start: připojení hybridního počítače se servery s podporou ARC Azure

[Servery s podporou ARC Azure](../overview.md) vám umožňují spravovat a řídit počítače s Windows a Linux hostovanými v místních, hraničních i cloudových prostředích. V tomto rychlém startu nasadíte a nakonfigurujete agenta připojeného počítače na počítači se systémem Windows nebo Linux, který je hostovaný mimo Azure pro správu prostřednictvím serverů s podporou ARC.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Nasazení serveru s povoleným ARC Agent pro hybridní připojení počítače vyžaduje, abyste na počítači měli oprávnění správce pro instalaci a konfiguraci agenta. V systému Linux, pomocí kořenového účtu a ve Windows, s účtem, který je členem místní skupiny Administrators.

* Než začnete, nezapomeňte si projít [požadavky](../agent-overview.md#prerequisites) agenta a ověřit následující:

    * Na cílovém počítači běží podporovaný [operační systém](../agent-overview.md#supported-operating-systems).

    * Vašemu účtu je přidělené přiřazení k [požadovaným rolím Azure](../agent-overview.md#required-permissions).

    * Pokud se počítač připojuje prostřednictvím brány firewall nebo proxy server komunikovat přes Internet, ujistěte se, že [uvedené](../agent-overview.md#networking-configuration) adresy URL nejsou blokované.

    * Servery s podporou ARC Azure podporují jenom oblasti, které jsou [tady](../overview.md#supported-regions)uvedené.

> [!WARNING]
> Název hostitele nebo počítač se systémem Linux nemůže v názvu použít jedno z vyhrazených slov nebo ochranných známek, jinak se pokus o registraci připojeného počítače k Azure nezdaří. Seznam rezervovaných slov najdete v tématu [řešení chyb rezervovaných názvů prostředků](../../../azure-resource-manager/templates/error-reserved-resource-name.md) .

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

## <a name="register-azure-resource-providers"></a>Registrovat poskytovatele prostředků Azure

Servery s podporou ARC Azure jsou závislé na následujících poskytovatelích prostředků Azure ve vašem předplatném, aby bylo možné tuto službu používat:

* Microsoft. HybridCompute
* Microsoft. GuestConfiguration

Zaregistrujte je pomocí následujících příkazů:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

## <a name="generate-installation-script"></a>Generovat instalační skript

Skript pro automatizaci stahování, instalace a navázání připojení ke službě Azure ARC je k dispozici z Azure Portal. Chcete-li dokončit proces, postupujte následovně:

1. Spusťte službu Azure ARC v Azure Portal kliknutím na **všechny služby** a pak vyhledáte a vyberete **servery – Azure ARC**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Hledat servery s podporou ARC ve všech službách" border="false":::

1. Na stránce **servery – ARC Azure** vyberte v levém horním rohu **Přidat** .

1. Na stránce **Vyberte metodu** vyberte dlaždici **Přidat servery pomocí interaktivního skriptu** a pak vyberte **vygenerovat skript**.

1. Na stránce **vygenerovat skript** vyberte předplatné a skupinu prostředků, ve které chcete, aby se počítač spravoval v rámci Azure. Vyberte umístění Azure, kam se budou ukládat metadata počítače. Toto umístění může být stejné nebo jiné jako umístění skupiny prostředků.

1. Na stránce **požadované součásti** zkontrolujte informace a pak vyberte **Další: podrobnosti o prostředku**.

1. Na stránce **Podrobnosti o prostředku** zadejte toto:

    1. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků, ze které se bude počítač spravovat.
    1. V rozevíracím seznamu **oblast** vyberte oblast Azure, do které se budou ukládat metadata serverů.
    1. V rozevíracím seznamu **operační systém** vyberte operační systém, na kterém je skript nakonfigurovaný ke spuštění.
    1. Pokud počítač komunikuje prostřednictvím proxy server pro připojení k Internetu, zadejte IP adresu proxy server nebo název a číslo portu, které bude počítač používat ke komunikaci s proxy server. Zadejte hodnotu ve formátu `http://<proxyURL>:<proxyport>` .
    1. Vyberte **Další: značky**.

1. Na stránce **značky** zkontrolujte vybrané výchozí **značky fyzického umístění** a zadejte hodnotu nebo zadejte jednu nebo více **vlastních značek** pro podporu standardů.

1. Vyberte **Další: Stáhněte a spusťte skript**.

1. Na stránce **Stáhnout a spustit skript** zkontrolujte souhrnné informace a pak vyberte **Stáhnout**. Pokud stále potřebujete dělat změny, vyberte **Předchozí**.

## <a name="install-the-agent-using-the-script"></a>Instalace agenta pomocí skriptu

### <a name="windows-agent"></a>Agent Windows

1. Přihlaste se k serveru.

1. Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními 64.

1. Přejděte do složky nebo sdílené složky, do které jste zkopírovali skript, a spusťte ho na serveru spuštěním `./OnboardingScript.ps1` skriptu.

### <a name="linux-agent"></a>Agent pro Linux

1. Pokud chcete nainstalovat agenta pro Linux do cílového počítače, který může přímo komunikovat s Azure, spusťte následující příkaz:

    ```bash
    bash ~/Install_linux_azcmagent.sh
    ```

    * Pokud cílový počítač komunikuje prostřednictvím proxy server, spusťte následující příkaz:

        ```bash
        bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
        ```

## <a name="verify-the-connection-with-azure-arc"></a>Ověření připojení k Azure Arcu

Po instalaci agenta a jeho konfiguraci pro připojení k serverům s podporou ARC Azure klikněte na Azure Portal a ověřte, že se server úspěšně připojil. Zobrazte počítač v [Azure Portal](https://aka.ms/hybridmachineportal).

:::image type="content" source="./media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Úspěšné připojení k počítači" border="false":::

## <a name="next-steps"></a>Další kroky

Teď, když jste povolili hybridní počítač se systémem Linux nebo Windows a úspěšně jste se připojili ke službě, jste připraveni povolit Azure Policy pro pochopení dodržování předpisů v Azure.

Pokud chcete zjistit, jak identifikovat počítač s povolenou podporou ARC Azure, na kterém není nainstalovaný agent Log Analytics, přejděte k kurzu:

> [!div class="nextstepaction"]
> [Vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy](tutorial-assign-policy-portal.md)
