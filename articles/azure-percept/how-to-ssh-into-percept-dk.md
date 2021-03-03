---
title: Připojení k Azure Percept DK přes SSH
description: Naučte se přes SSH do Azure Percept DK s použitím
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/03/2021
ms.custom: template-how-to
ms.openlocfilehash: 8d150228be2cf6deff3bc2fd0a0599cca70d24ac
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662381"
---
# <a name="connect-to-your-azure-percept-dk-over-ssh"></a>Připojení k Azure Percept DK přes SSH

Podle následujících pokynů nastavte připojení SSH ke službě Azure Percept DK prostřednictvím [výstupu](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

## <a name="prerequisites"></a>Požadavky

- Hostitelský počítač se systémem Windows, Linux nebo OS X s funkcí Wi-Fi
- Klient SSH
    - Pokud hostitelský [počítač používá systém Windows, je k](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) dismístě platný klient SSH a bude použit v rámci této příručky.
    - Pokud hostitelský počítač používá Linux nebo OS X, jsou služby SSH součástí těchto operačních systémů a lze je spustit bez samostatné klientské aplikace. Další informace o tom, jak spustit služby SSH, najdete v dokumentaci k produktu s operačním systémem.
- Azure Percept DK

## <a name="initiate-the-ssh-connection"></a>Iniciujte připojení SSH.

1. Zapněte si Azure Percept DK (dev Kit).

1. Pokud je vaše sada dev SDK už připojená k síti přes Ethernet nebo Wi-Fi, přejděte k dalšímu kroku. V opačném případě připojte hostitelský počítač přímo k Wi-Fi přístupovému bodu vývojářské sady, stejně jako připojení k jiné síti Wi-Fi:
    - **název sítě**: SCZ-xxxx (kde "XXXX" jsou poslední čtyři číslice síťové adresy MAC sady dev Kit.)
    - **heslo**: dá se najít na úvodní kartě, která se dodává se sadou dev Kit.

    > [!WARNING]
    > I když jste připojení k Azure Percept Wi-Fi DK přístupového bodu, váš hostitelský počítač dočasně ztratí připojení k Internetu. Aktivní videokonference, volání na webu nebo jiná síťová prostředí budou přerušeny, dokud se nedokončí krok 3 v prostředí Azure Percept DK na zprovoznění.

1. Otevřete PuTTY. Zadejte následující příkaz a klikněte na tlačítko **otevřít** do svého devkitu v protokolu SSH:

    1. Název hostitele: 10.1.1.1
    1. Port: 22
    1. Typ připojení: SSH

    > [!NOTE]
    > **Název hostitele** je IP adresa vašeho zařízení. Pokud je vaše sada dev SDK připojená k Wi-Fi přístupovému bodu sady pro vývojáře, IP adresa bude 10.1.1.1. Pokud je vaše sada dev SDK připojená přes síť Ethernet, použijte místní IP adresu zařízení, kterou můžete získat ze směrovače nebo centra Ethernet. Pokud je zařízení připojené přes Wi-Fi, musíte použít IP adresu, kterou jste shromáždili během [prostředí Azure PERCEPT DK pro zprovoznění](./quickstart-percept-dk-set-up.md).

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/ssh-putty.png" alt-text="Obrazu.":::

1. Přihlaste se k terminálu pro výstup. Pokud během procesu OOBE nastavíte uživatelské jméno a heslo SSH, po zobrazení výzvy zadejte přihlašovací údaje. V opačném případě zadejte následující:  

    1. přihlášení jako: root
    1. Heslo: p@ssw0rd

    :::image type="content" source="./media/how-to-ssh-into-percept-dk/putty-terminal.png" alt-text="Okno terminálu pro výstup.":::  

## <a name="next-steps"></a>Další kroky

Po úspěšném připojení k Azure Percept DK prostřednictvím protokolu SSH můžete provádět různé úlohy, včetně řešení potíží, aktualizací USB a spuštění nástroje DiagTool nebo SoftAP.


