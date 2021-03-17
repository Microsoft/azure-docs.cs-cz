---
title: Nastavení Azure Percept DK
description: Připojte si vývojářskou sadu k Azure a nasaďte svůj první model AI.
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/15/2021
ms.custom: template-quickstart
ms.openlocfilehash: 49bf89d38edef6a9186cbdb5bb89a763339385b4
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102175817"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Nastavte si Azure Percept DK a nasaďte svůj první model AI.

Začínáme s Azure Percept DK a Azure Percept Studio pomocí prostředí pro instalaci Azure Percept DK k připojení zařízení k Azure a nasazení vašeho prvního modelu AI. Po ověření, že je váš účet Azure kompatibilní s Azure Percept Studio, dokončete prostředí pro instalaci, abyste zajistili, že vaše Azure Percept DK je nakonfigurovaná tak, aby se vytvořila kontrola konceptů.

Pokud dojde k jakýmkoli problémům během tohoto rychlé zprovoznění, vyhledejte možná řešení v průvodci [odstraňováním potíží](./troubleshoot-dev-kit.md) .

## <a name="prerequisites"></a>Požadavky

- Azure Percept DK.
- Hostitelský počítač se systémem Windows, Linux nebo OS X se schopností Wi-Fi a webovým prohlížečem.
- Účet Azure s aktivním předplatným. [Vytvořit účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Účet Azure musí mít v předplatném roli Vlastník nebo Přispěvatel. Přečtěte si další informace o [definicích rolí Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

Pokud chcete ověřit, jestli je váš účet Azure v předplatném vlastník nebo přispěvatel, postupujte podle těchto kroků.

1. Přejít na Azure Portal a přihlaste se pomocí stejného účtu Azure, který máte v úmyslu používat s Azure Percept Studio. 

    > [!NOTE]
    > Pokud máte více účtů Azure, může váš prohlížeč ukládat přihlašovací údaje z jiného účtu. Další informace o tom, jak zajistit, že jste se přihlásili ke správnému účtu, najdete v Průvodci odstraňováním potíží.

1. Rozbalte hlavní nabídku v levém horním rohu obrazovky a klikněte na odběry nebo vyberte odběry z nabídky ikon na domovské stránce. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-01-subscription.png" alt-text="supscription icon in Azure portal.":::
    --->
1. V seznamu vyberte své předplatné. Pokud v seznamu nevidíte své předplatné, ujistěte se, že jste přihlášeni pomocí správného účtu Azure. 
    <!---
    :::image type="content" source="./media/quickstart-percept-dk-setup/prereq-02-sub-list.png" alt-text="supscription list in Azure portal.":::
    --->
Pokud chcete vytvořit nové předplatné, postupujte podle [těchto kroků](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription).

1. V nabídce předplatné vyberte řízení přístupu (IAM).
1. Klikněte na tlačítko Zobrazit můj přístup.
1. Kontrolovat roli
    - Pokud se zobrazí role čtenář nebo když se zobrazí zpráva s oznámením, že nemáte oprávnění k zobrazení rolí, budete muset ve vaší organizaci postupovat podle potřebného procesu, aby se vám pozměnila role účtu.
    - Pokud se role zobrazuje jako vlastník nebo přispěvatel, váš účet bude fungovat s Azure Percept Studio. 

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Spustit prostředí pro instalaci Azure Percept DK

<!---
> [!NOTE]
> Connecting over ethernet? See [this how-to guide](<link needed>) for detailed instructions.
--->
1. Připojte svůj hostitelský počítač přímo k přístupovému bodu sítě Wi-Fi pro vývojovou sadu. To se provádí stejně jako připojení k jakékoli jiné síti Wi-Fi.
    - **název sítě**: SCZ-xxxx (kde "XXXX" jsou poslední čtyři číslice síťové adresy MAC sady dev Kit.)
    - **heslo**: dá se najít na úvodní kartě, která se dodává se sadou dev Kit.

    > [!WARNING]
    > I když jste připojeni k přístupovému bodu Wi-Fi v Azure Percept, váš hostitelský počítač dočasně ztratí připojení k Internetu. Aktivní videokonference, volání na webu nebo jiné síťové prostředí bude přerušeno, dokud se nedokončí krok 3 v prostředí pro instalaci Azure Percept DK.

1. Po připojení k přístupovému bodu Wi-Fi sady dev Kit bude hostitelské zařízení automaticky spouštět prostředí pro instalaci Azure Percept DK v novém okně prohlížeče. Pokud se automaticky neotevře, můžete ho spustit ručně otevřením okna prohlížeče a přechodem na [http://10.1.1.1](http://10.1.1.1) . 

1. Teď, když jste spustili prostředí pro instalaci Azure Percept, jste připraveni pokračovat v prostředí pro instalaci. 

    > [!NOTE]
    > Webová služba prostředí pro instalaci Azure Percept DK se vypne po 30 minutách nepoužívání a po dokončení instalačního prostředí. Pokud k tomu dojde, doporučuje se restartovat vývojovou sadu, aby se předešlo problémům s připojením k přístupovému bodu sítě Wi-Fi sady dev SDK.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Dokončete prostředí pro instalaci Azure Percept DK

1. Začněte – na úvodní obrazovce klikněte na **Další** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Úvodní stránka.":::

1. Připojení k Wi-Fi – na stránce připojení k síti klikněte na **připojit k nové síti Wi** -Fi, abyste připojili své DevKit k síti Wi-Fi.

    Pokud jste tuto vývojářskou sadu dříve připojili k vaší síti Wi-Fi nebo pokud jste už připojení k prostředí nastavení Azure Percept DK prostřednictvím sítě Wi-Fi, klikněte na odkaz **Přeskočit** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-02-connect-to-wi-fi.png" alt-text="Připojte se k Wi-Fi.":::

1. Vyberte síť Wi-Fi z dostupných připojení a připojte se. (Bude vyžadovat vaše místní heslo Wi-Fi)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-03-select-wi-fi.png" alt-text="Vyberte síť Wi-Fi."::: 

1. Zkopírování IP adresy – Jakmile se vaše DevKit úspěšně připojí k vaší síti, zapište **adresu IPv4** zobrazenou na stránce. **Tuto IP adresu budete potřebovat později v této úvodní příručce**. 

    > [!NOTE]
    > IP adresa se může změnit při každém spuštění zařízení.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="Zkopírujte IP adresu.":::

1. Přečtěte si a přijměte licenční smlouvu – přečtěte si licenční smlouvu, vyberte si, že **jste si přečetli licenční smlouvu a souhlasíte s nimi** (musí se posunout na konec smlouvy) a klikněte na **Další**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="Přijmout smlouvu EULA.":::

1. Vytvoření přihlašovacího účtu SSH – nastavte SSH pro vzdálený přístup k vašemu DevKit. Vytvořte uživatelské jméno a heslo SSH. 

    > [!NOTE]
    > SSH (Secure Shell) je síťový protokol používaný pro zabezpečenou komunikaci mezi hostitelským zařízením a vývojářskou sadou. Další informace o SSH najdete v [tomto článku](https://en.wikipedia.org/wiki/SSH_(Secure_Shell)).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-06-ssh-login.png" alt-text="Vytvořte účet SSH."::: 

1. Zahajte proces připojení sady dev Kit – na další obrazovce klikněte na **připojit k novému zařízení** a zahajte proces připojení sady pro vývoj k Azure IoT Hub. 

    <!---
    Connecting with an existing IoT Edge device connection string? See this [how-to guide](<link needed>) for reference.
    --->
    :::image type="content" source="./media/quickstart-percept-dk-setup/main-07-connect-device.png" alt-text="Připojte se k Azure."::: 

1. Zkopírování kódu zařízení – kliknutím na odkaz **Kopírovat** zkopírujte kód zařízení. Pak klikněte na **Přihlásit se k Azure**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Zkopírujte kód zařízení."::: 

1. Vložení kódu zařízení – otevře se nová karta prohlížeče s oknem, které se zeptá na kopírovaný kód zařízení. Vložte kód do okna a klikněte na tlačítko **Další**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Zadejte kód zařízení."::: 

1. Přihlášení k Azure – další okno vyžaduje, abyste se přihlásili pomocí účtu Azure, který jste ověřili na začátku rychlé zprovoznění. Zadejte přihlašovací údaje pro přihlášení a klikněte na **Další**. 

    > [!NOTE]
    > Váš prohlížeč může automaticky ukládat do mezipaměti další přihlašovací údaje. Dvakrát ověřte, že se přihlašujete pomocí správného účtu.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-10-azure-sign-in.png" alt-text="Přihlaste se k Azure.":::
 
1. Nezavírejte na tomto kroku kartu prohlížeče Experience pro instalaci. po přihlášení se zobrazí obrazovka s potvrzením, že jste se přihlásili. I když říkáte, že okno zavřete, doporučujeme **, abyste nezavřeli žádná okna**. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-11-sign-in-success.png" alt-text="Přihlášení bylo úspěšné."::: 

1. Vraťte se na kartu prohlížeče hostující prostředí instalace.
1. Vyberte možnost IoT Hub
    - Pokud již máte IoT Hub a je uvedena na této stránce, můžete ji vybrat a **Přejít ke kroku 17**.
    - Pokud nemáte IoT Hub nebo chcete vytvořit novou, přejděte na konec seznamu a klikněte na **vytvořit novou IoT Hub Azure**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Vyberte IoT Hub."::: 

1. Vytvořte novou IoT Hub – vyplňte všechna pole na této stránce. 
    - Vyberte předplatné Azure, které budete používat se službou Azure Percept Studio.
    - Vyberte existující skupinu prostředků. Pokud jeden neexistuje, klikněte na vytvořit nový a postupujte podle pokynů. 
    - Vyberte oblast Azure. 
    - Zadejte nový IoT Hub název
    - Výběr cenové úrovně (obvykle se bude shodovat s předplatným)

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-14-create-iot-hub.png" alt-text="Vytvoří nový IoT Hub."::: 

1. Počkat na nasazení IoT Hub – může to trvat několik minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-15-iot-hub-deploy.png" alt-text="Nasazení IoT Hub."::: 

1. Zaregistrovat vývojovou sadu – po dokončení nasazení klikněte na tlačítko **Registrovat** .

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub se úspěšně nasadilo."::: 

1. Pojmenujte sadu dev Kit – zadejte název zařízení pro vývojáře a klikněte na **Další**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-17-device-name.png" alt-text="Pojmenujte zařízení."::: 

1. Počkat na stažení výchozích modelů AI – Tato akce bude trvat několik minut.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Dokončuje se instalace."::: 

1. Viz Vision AI v akci – váš DevKit se úspěšně připojil k vašemu Azure IoT Hub a získal výchozí model detekce objektů AI. Kamera ve službě Azure Percept Vision teď může Inferencing rozpoznávat objekty bez připojení ke cloudu. 

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-2-preview-video-output.png" alt-text="Klikněte na náhled výstup videa."::: 
       
    > [!NOTE]
    > Proces zprovoznění a připojení zařízení WiFi přístup k vašemu hostitelskému počítači se v tomto okamžiku vypne, ale vaše vývojová sada zůstane připojená k Internetu.   Můžete restartovat prostředí pro zprovoznění pomocí nástroje pro vývoj v sadě pro vývojáře, který vám umožní přejít zpět k registraci zařízení a znovu ho připojit k jinému centru IOT přidruženému ke stejnému nebo jinému předplatnému Azure.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Upozornění na odpojení prostředí instalačního programu."::: 

1. Pokračujte Azure Portal – vraťte se do okna Možnosti instalace a klikněte na tlačítko **pokračovat k Azure Portal** a začněte vytvářet vlastní modely AI v Azure Percept studiu.

    > [!NOTE]
    > Ověřte, že váš hostitelský počítač už není připojený k přístupovému bodu sady pro vývojáře v nastavení Wi-Fi a že se teď znovu připojí k místní síti Wi-Fi.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Přejít na Azure Percept Studio."::: 

## <a name="view-your-device-in-the-azure-percept-studio-and-deploy-common-prebuilt-sample-apps"></a>Zobrazení zařízení v Azure Percept studiu a nasazení běžných předem připravených ukázkových aplikací

1. Zobrazte si seznam zařízení na stránce s přehledem [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) . Stránka s přehledem Azure Percept je váš bod spuštění pro přístup k mnoha různým pracovním postupům pro počáteční i pokročilý model a vývoj řešení AI.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Zobrazte si seznam zařízení.":::
    
1. Ověřte, jestli je zařízení, které jste právě vytvořili, připojené, a klikněte na něj.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Vyberte své zařízení.":::

1. Podívejte se na datový proud zařízení, abyste viděli, co vaše kamera vývojářské sady zobrazuje. Výchozí model detekce objektu se nasadí mimo pole a detekuje několik běžných objektů.

    > [!NOTE]
    > Když to uděláte poprvé na novém zařízení, zobrazí se oznámení, že nový modul se nasazuje v pravém horním rohu.  Po konkurenci budete moct okno spustit s datovým proudem videa kamery. 
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Podívejte se na video stream.":::
    
    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Viz detekce objektu.":::

1. Prozkoumejte předem připravené ukázkové modely AI.   Azure Precept Studio má řadu běžných předem připravených ukázek, které je možné nasadit jediným kliknutím.   Vyberte nasadit ukázkový model a zobrazte a nasaďte je.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Prozkoumejte předem připravené modely.":::
    
1. Nasaďte na připojené zařízení novou předem vytvořenou ukázku. Vyberte ukázku z knihovny a klikněte na nasadit do zařízení.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Viz detekce objektu v akci.":::

## <a name="next-steps"></a>Další kroky

Pomocí podobného toku můžete vyzkoušet [předem připravené modely řeči](./tutorial-no-code-speech.md).