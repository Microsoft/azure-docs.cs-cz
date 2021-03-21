---
title: Nastavení Azure Percept DK
description: Připojte si vývojářskou sadu k Azure a nasaďte svůj první model AI.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 9567ec2458a01825568cb853728f71db10228ee3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608254"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Nastavte si Azure Percept DK a nasaďte svůj první model AI.

Dokončete prostředí instalace Azure Percept DK a nakonfigurujte sadu dev SDK a nasaďte svůj první model AI. Po ověření, že je váš účet Azure kompatibilní s Azure Percept, budete:

- Připojení sady pro vývoj k Wi-Fi síti
- Nastavení přihlášení SSH pro vzdálený přístup k sadě dev SDK
- Vytvoření nové IoT Hub pro použití s Azure Percept
- Připojte sadu dev SDK k vašemu IoT Hub a účtu Azure

Pokud během tohoto procesu dojde k problémům, vyhledejte možná řešení v [Průvodci odstraňováním potíží s instalací](./how-to-troubleshoot-setup.md) .

## <a name="prerequisites"></a>Předpoklady

- Azure Percept DK (dev Kit).
- Hostitelský počítač se systémem Windows, Linux nebo OS X s možností Wi-Fi a webovým prohlížečem.
- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Účet Azure musí mít v rámci předplatného roli **vlastníka** nebo **přispěvatele** . Pomocí následujících kroků ověřte roli účtu Azure. Další informace o definicích rolí Azure najdete v dokumentaci k [řízení přístupu na základě role v Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

    > [!CAUTION]
    > Pokud máte více účtů Azure, může váš prohlížeč ukládat přihlašovací údaje z jiného účtu. Aby nedocházelo k nejasnostem, doporučujeme, abyste před spuštěním instalačního programu zavřeli všechna nepoužívaná okna prohlížeče a přihlásili se k [Azure Portal](https://portal.azure.com/) . Další informace o tom, jak zajistit, že jste se přihlásili ke správnému účtu, najdete v [Průvodci řešením potíží s instalací](./how-to-troubleshoot-setup.md) .

### <a name="check-your-azure-account-role"></a>Kontrolovat roli účtu Azure

Pokud chcete ověřit, jestli je váš účet Azure v rámci předplatného "vlastník" nebo "Přispěvatel", postupujte podle následujících kroků:

1. Přejít na [Azure Portal](https://portal.azure.com/) a přihlaste se pomocí stejného účtu Azure, který máte v úmyslu používat se službou Azure Percept.

1. Klikněte na ikonu **odběry** (vypadá to jako žlutý klíč).

1. V seznamu vyberte své předplatné. Pokud vaše předplatné nevidíte, ujistěte se, že jste přihlášeni pomocí správného účtu Azure. Pokud chcete vytvořit nové předplatné, postupujte podle [těchto kroků](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. V nabídce předplatné vyberte **řízení přístupu (IAM)**.
1. Klikněte na **Zobrazit můj přístup**.
1. Ověřte roli:
    - Pokud je vaše role uvedená jako **Čtenář** nebo pokud se zobrazí zpráva s oznámením, že nemáte oprávnění k zobrazení rolí, budete muset při zvýšení úrovně role účtu použít nezbytný postup ve vaší organizaci.
    - Pokud je vaše role uvedená jako **vlastník** nebo **Přispěvatel**, váš účet bude fungovat s Azure Percept a můžete pokračovat v instalaci.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Spustit prostředí pro instalaci Azure Percept DK

1. Připojte hostitelský počítač přímo k Wi-Fi přístupovému bodu sady dev Kit. Chcete-li se připojit k libovolné jiné Wi-Fi síti, otevřete v počítači nastavení sítě a Internetu, klikněte na následující síť a po zobrazení výzvy zadejte heslo k síti:

    - **Název sítě**: v závislosti na verzi operačního systému pro vývojovou sadu je název Wi-Fiho bodu přístupu buď **SCZ-xxxx** , nebo **APD-xxxx** (kde "XXXX" jsou poslední čtyři číslice adresy MAC sady dev Kit.)
    - **Heslo**: dá se najít na úvodní kartě, která se dodává se sadou dev Kit.

    > [!WARNING]
    > I když jste připojení k Azure Percept Wi-Fi DK přístupového bodu, váš hostitelský počítač dočasně ztratí připojení k Internetu. Aktivní videokonference, streamování na webu nebo jiné síťové prostředí se přeruší.

1. Po připojení k Wi-Fi přístupového bodu sady dev Kit bude hostitelský počítač automaticky spouštět prostředí pro instalaci v novém okně prohlížeče s **. New. Device/** v adresním řádku. Pokud se karta neotevře automaticky, spusťte instalační program tak, že přejdete na [http://10.1.1.1](http://10.1.1.1) . Ujistěte se, že je váš prohlížeč přihlášený se stejnými přihlašovacími údaji účtu Azure, které máte v úmyslu používat s Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Úvodní stránka.":::

    > [!CAUTION]
    > Webová služba Experience pro instalaci se ukončí po 30 minutách nepoužívání. Pokud k tomu dojde, restartujte vývojovou sadu, abyste se vyhnuli problémům s připojením k Wi-Fimu přístupovému bodu sady pro vývojáře.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Dokončete prostředí pro instalaci Azure Percept DK

1. Na **úvodní** obrazovce klikněte na **Další** .

1. Na stránce **připojení k síti** klikněte na **připojit k nové síti Wi-Fi**.

    Pokud jste už vývojářskou sadu připojili k vaší Wi-Fi síti, klikněte na **Přeskočit**.

1. Ze seznamu dostupných sítí vyberte svou Wi-Fiovou síť a klikněte na **připojit**. Po zobrazení výzvy zadejte své síťové heslo.

1. Po úspěšném připojení sady pro vývojáře k vaší síti, na stránce se zobrazí adresa IPv4 přiřazená k sadě pro vývojáře. **Zapište adresu IPv4 zobrazenou na stránce.** Pro řešení potíží a aktualizace zařízení budete potřebovat IP adresu při připojování k vývojové sadě přes protokol SSH.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Zkopírujte IP adresu.":::

    > [!NOTE]
    > IP adresa se může změnit při každém spuštění zařízení.

1. Přečtěte si licenční smlouvu, vyberte, že **jste si přečetli licenční smlouvu a souhlasíte** s ní (musíte se posouvat do dolní části smlouvy) a kliknout na **Další**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Přijmout smlouvu EULA.":::

1. Zadejte název a heslo účtu SSH a **zapište přihlašovací údaje pro pozdější použití**.

    > [!NOTE]
    > SSH (Secure Shell) je síťový protokol, který umožňuje vzdálené připojení k vývojové sadě prostřednictvím hostitelského počítače.

1. Na další stránce klikněte na **nastavit jako nové zařízení** a vytvořte tak nové zařízení v rámci svého účtu Azure.

1. Kliknutím na **Kopírovat** zkopírujte kód vašeho zařízení. Potom klikněte na **Přihlásit se k Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Zkopírujte kód zařízení.":::

1. Otevře se nová karta prohlížeče s oknem, ve kterém se říká **ENTER Code**. Vložte kód do okna a klikněte na tlačítko **Další**. Nezavírejte kartu **Vítejte** s prostředím nastavení.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Zadejte kód zařízení.":::

1. Přihlaste se k Azure Percept pomocí přihlašovacích údajů k účtu Azure, které použijete v sadě SDK pro vývojáře. Po dokončení ponechte kartu prohlížeč otevřenou.

    > [!CAUTION]
    > Váš prohlížeč může automaticky ukládat do mezipaměti další přihlašovací údaje. Dvakrát ověřte, že se přihlašujete pomocí správného účtu.

    Po úspěšném přihlášení do Azure procent na zařízení se vraťte na kartu **Vítejte** a pokračujte v instalaci.

1. Po zobrazení stránky **přiřazení zařízení k vaší službě Azure IoT Hub** na kartě **Vítejte** proveďte jednu z následujících akcí:

    - Pokud už máte IoT Hub chcete použít se službou Azure Percept a je uvedená na této stránce, vyberte ji a přejděte ke kroku 15.
    - Pokud nemáte IoT Hub nebo chcete vytvořit nový, klikněte na **vytvořit nový IoT Hub Azure**.

    > [!IMPORTANT]
    > Pokud máte IoT Hub, ale v seznamu se nezobrazuje, možná jste se k Azure Percept přihlásili pomocí nesprávných přihlašovacích údajů. Nápovědu najdete v [Průvodci odstraňováním potíží s instalací](./how-to-troubleshoot-setup.md) .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Vyberte IoT Hub.":::

1. Chcete-li vytvořit nový IoT Hub, vyplňte následující pole:

    - Vyberte předplatné Azure, které budete používat se službou Azure Percept.
    - Vyberte existující skupinu prostředků. Pokud jeden neexistuje, klikněte na **vytvořit nový** a postupujte podle pokynů.
    - Vyberte oblast Azure, která je nejblíže vašemu fyzickému umístění.
    - Zadejte nový IoT Hub název.
    - Vyberte cenovou úroveň S1 (Standard).

    > [!NOTE]
    > Pokud pro aplikace Edge AI potřebujete vyšší [propustnost zpráv](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) , můžete kdykoli [upgradovat IoT Hub na vyšší úroveň Standard](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) na portálu Azure Portal. Vrstvy B a F nepodporují Azure Percept.

1. Nasazení IoT Hub může trvat několik minut. Po dokončení nasazení klikněte na **zaregistrovat**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub se úspěšně nasadilo.":::

1. Zadejte název zařízení pro vývojovou sadu a klikněte na **Další**.

1. Počkejte, až se stáhnou moduly zařízení – to může trvat několik minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Dokončuje se instalace.":::

1. Po zobrazení **nastavení zařízení se dokončí.** , vaše sada dev SDK se úspěšně připojila k vašemu IoT Hub a stáhla potřebný software. Vaše sada pro vývoj se automaticky odpojí od Wi-Fi přístupového bodu, což vede k těmto dvěma oznámením:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Upozornění na odpojení prostředí instalačního programu.":::

1. Připojte hostitelský počítač k síti Wi-Fi, ke které jste v DevKit připojili v kroku 2.

1. Klikněte na **pokračovat na Azure Portal**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Přejít na Azure Percept Studio.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Zobrazení streamu videa sady dev Kit a nasazení ukázkového modelu

1. [Stránka s přehledem Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) je váš bod spuštění pro přístup k mnoha různým pracovním postupům pro vývoj řešení pro počáteční i pokročilý hraniční systém AI. Začněte tím, že v nabídce vlevo kliknete na **zařízení** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Zobrazte si seznam zařízení.":::

1. Ověřte, že je vaše sada dev SDK uvedena jako **připojená** a kliknutím na ni zobrazte stránku zařízení.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Vyberte své zařízení.":::

1. Klikněte na **Zobrazit datový proud zařízení**. Pokud si video stream svého zařízení poprvé prohlížíte, zobrazí se oznámení, že nový model se nasazuje v pravém horním rohu. Může to trvat několik minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Podívejte se na video stream.":::

    Po nasazení modelu se zobrazí další oznámení s odkazem na **Stream zobrazení** . Kliknutím na odkaz zobrazíte Stream videa z kamery Azure Percept Vision v novém okně prohlížeče. Sada dev SDK je předem načtena s modelem AI, který automaticky provádí detekci objektů mnoha běžných objektů.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Viz detekce objektu.":::

1. Azure Percept Studio má také řadu ukázkových modelů AI. Pokud chcete nasadit Vzorový model do sady dev SDK, přejděte zpátky na stránku zařízení a klikněte na **nasadit ukázkový model**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Prozkoumejte předem připravené modely.":::

1. V knihovně vyberte Vzorový model a klikněte na **nasadit do zařízení**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Viz detekce objektu v akci.":::

1. Po úspěšném nasazení modelu se zobrazí oznámení s odkazem na **Stream zobrazení** v pravém horním rohu obrazovky. Pokud chcete zobrazit Inferencing modelu v akci, klikněte na odkaz v oznámení nebo se vraťte na stránku zařízení a klikněte na **Zobrazit datový proud zařízení**. Všechny modely dřív spuštěné v sadě dev Kit se teď nahradí novým modelem.

## <a name="video-walkthrough"></a>Video s návodem

Pro vizuální návod kroků popsaných výše se prosím podívejte na následující video (spouštěcí prostředí začíná na 0:50):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření řešení pro vize bez kódu](./tutorial-nocode-vision.md)