---
title: Připojení k Azure Percept DK přes SSH
description: Naučte se přes SSH do Azure Percept DK s použitím
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 39ee1c1cc5b52dc62e3199536234c1f7d9381436
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721473"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Připojení k Azure Percept DK přes SSH

Postupujte podle následujících kroků a nastavte připojení SSH ke službě Azure Percept DK prostřednictvím OpenSSH nebo [výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Požadavky

- Hostitelský počítač se systémem Windows, Linux nebo OS X s funkcí Wi-Fi
- Klient SSH (pokyny k instalaci najdete v další části)
- Azure Percept DK (Vývojářská sada)
- Přihlášení SSH vytvořené během [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md)

## <a name="install-your-preferred-ssh-client"></a>Instalace preferovaného klienta SSH

Pokud hostitelský počítač používá Linux nebo OS X, jsou služby SSH součástí těchto operačních systémů a lze je spustit bez samostatné klientské aplikace. Další informace o tom, jak spustit služby SSH, najdete v dokumentaci k produktu s operačním systémem.

Pokud hostitelský počítač používá systém Windows, můžete si vybrat ze dvou možností klienta SSH: OpenSSH a.

### <a name="openssh"></a>OpenSSH

Windows 10 obsahuje integrovaného klienta SSH s názvem OpenSSH, který se dá spustit pomocí jednoduchého příkazu v příkazovém řádku. Pokud máte k dispozici, doporučujeme používat OpenSSH s Azure Percept. Pokud chcete zjistit, jestli je v počítači s Windows nainstalovaný OpenSSH, postupujte takto:

1. Přejít na **začátek**  ->  **Nastavení**.

1. Vyberte **aplikace**.

1. V části **aplikace & funkce** vyberte **volitelné funkce**.

1. Do panelu hledání **nainstalovaných funkcí** zadejte **OpenSSH Client** . Pokud se zobrazí OpenSSH, klient už je nainstalovaný a můžete přejít k další části. Pokud nevidíte OpenSSH, klikněte na **Přidat funkci**.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-install.png" alt-text="Snímek obrazovky s nastavením, který zobrazuje stav instalace OpenSSH":::

1. Vyberte **OpenSSH Client** a klikněte na **nainstalovat**. Nyní můžete přejít k další části. Pokud OpenSSH není k dispozici pro instalaci na váš počítač, postupujte podle následujících kroků a nainstalujte k němu klienta SSH jiného výrobce.

### <a name="putty"></a>PuTTY

Pokud Váš počítač s Windows nezahrnuje OpenSSH, doporučujeme použít výstup do [výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html). Chcete-li stáhnout a nainstalovat výstup, proveďte následující kroky:

1. Přejít na [stránku pro stažení do výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

1. V části **soubory balíčku** klikněte na 32 nebo 64 soubor. msi a stáhněte instalační program. Pokud si nejste jistí, kterou verzi si zvolíte, Projděte si [Nejčastější dotazy](https://www.chiark.greenend.org.uk/~sgtatham/putty/faq.html#faq-32bit-64bit).

1. Kliknutím na instalační program spusťte proces instalace. Podle potřeby postupujte podle pokynů.

1. Gratulujeme! Úspěšně jste nainstalovali klienta SSH pro výstup.

## <a name="initiate-the-ssh-connection"></a>Iniciujte připojení SSH.

1. Zapněte si Azure Percept DK.

1. Pokud je vaše sada dev SDK už připojená k síti přes Ethernet nebo Wi-Fi, přejděte k dalšímu kroku. V opačném případě připojte hostitelský počítač přímo k Wi-Fi přístupovému bodu sady dev Kit. Chcete-li se připojit k libovolné jiné Wi-Fi síti, otevřete v počítači nastavení sítě a Internetu, klikněte na následující síť a po zobrazení výzvy zadejte heslo k síti:

    - **Název sítě**: v závislosti na verzi operačního systému pro vývojovou sadu je název Wi-Fiho bodu přístupu buď **SCZ-xxxx** , nebo **APD-xxxx** (kde "XXXX" jsou poslední čtyři číslice adresy MAC sady dev Kit.)
    - **Heslo**: dá se najít na úvodní kartě, která se dodává se sadou dev Kit.

    > [!WARNING]
    > I když jste připojení k Azure Percept Wi-Fi DK přístupového bodu, váš hostitelský počítač dočasně ztratí připojení k Internetu. Aktivní videokonference, streamování na webu nebo jiné síťové prostředí se přeruší.

1. Dokončete proces připojení SSH podle vašeho klienta SSH.

### <a name="using-openssh"></a>Použití OpenSSH

1. Otevřete příkazový řádek (**Spusťte** příkazový  ->  **řádek**).

1. Do příkazového řádku zadejte následující:

    ```console
    ssh [your ssh user name]@[IP address]
    ```

    Pokud je váš počítač připojený k Wi-Fi přístupovému bodu sady pro vývojáře, IP adresa bude 10.1.1.1. Pokud je vaše sada dev SDK připojená přes síť Ethernet, použijte místní IP adresu zařízení, kterou můžete získat ze směrovače nebo centra Ethernet. Pokud je vaše vývojová sada připojená přes Wi-Fi, musíte použít IP adresu, která byla přiřazena k sadě dev SDK během [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Pokud je vaše sada pro vývoj připojená k síti Wi-Fi, ale neznáte její IP adresu, navštivte Azure Percept Studio a [otevřete Stream videa vašeho zařízení](./how-to-view-video-stream.md). V adresním řádku na kartě prohlížeče streamu videa se zobrazí IP adresa vašeho zařízení.

1. Po zobrazení výzvy zadejte své heslo SSH.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/open-ssh-prompt.png" alt-text="Snímek obrazovky s otevřeným přihlášením příkazového řádku SSH":::

1. Pokud se jedná o první připojení k vývojové sadě prostřednictvím OpenSSH, může se také zobrazit výzva, abyste přijali klíč hostitele. Zadáním **Ano** přijměte klíč.

1. Gratulujeme! Úspěšně jste se připojili k sadě dev SDK přes SSH.

### <a name="using-putty"></a>Používání výstupu

1. Otevřete PuTTY. Do okna **Konfigurace** pro výstupy zadejte následující a klikněte na **otevřít** k SSH do sady dev SDK:

    1. Název hostitele: [IP adresa]
    1. Port: 22
    1. Typ připojení: SSH

    **Název hostitele** je IP adresa sady pro vývojáře. Pokud je váš počítač připojený k Wi-Fi přístupovému bodu sady pro vývojáře, IP adresa bude 10.1.1.1. Pokud je vaše sada dev SDK připojená přes síť Ethernet, použijte místní IP adresu zařízení, kterou můžete získat ze směrovače nebo centra Ethernet. Pokud je vaše vývojová sada připojená přes Wi-Fi, musíte použít IP adresu, která byla přiřazena k sadě dev SDK během [prostředí pro instalaci Azure PERCEPT DK](./quickstart-percept-dk-set-up.md).

    > [!TIP]
    > Pokud je vaše sada pro vývoj připojená k síti Wi-Fi, ale neznáte její IP adresu, navštivte Azure Percept Studio a [otevřete Stream videa vašeho zařízení](./how-to-view-video-stream.md). V adresním řádku na kartě prohlížeče streamu videa se zobrazí IP adresa vašeho zařízení.

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Snímek obrazovky okna Konfigurace výstupu.":::

1. Otevře se terminál výstupu. Po zobrazení výzvy zadejte své uživatelské jméno a heslo SSH do terminálu.

1. Gratulujeme! Úspěšně jste se připojili k sadě dev SDK přes SSH.

## <a name="next-steps"></a>Další kroky

Po připojení ke službě Azure Percept DK prostřednictvím protokolu SSH můžete provádět různé úlohy, včetně [řešení potíží se zařízením](./troubleshoot-dev-kit.md) a [aktualizací USB](./how-to-update-via-usb.md).