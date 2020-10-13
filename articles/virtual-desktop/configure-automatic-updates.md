---
title: Konfigurace Microsoft Endpoint Configuration Manager – Azure
description: Postup konfigurace Configuration Manager koncového bodu Microsoft pro nasazení aktualizací softwaru do vícenásobné relace Windows 10 Enterprise na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 06/12/2020
ms.author: helohr
ms.reviewer: v-cawood; clemr
manager: lizross
ms.openlocfilehash: bfc797e0c11a1804d27c6c97fea8cb2ba62dca7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010119"
---
# <a name="configure-microsoft-endpoint-configuration-manager"></a>Nakonfigurovat Configuration Manager koncového bodu Microsoft

Tento článek vysvětluje, jak nakonfigurovat službu Microsoft Endpoint Configuration Manager tak, aby automaticky používala aktualizace pro hostitele virtuálních ploch Windows s Windows 10 Enterprise s více relacemi.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci tohoto nastavení budete potřebovat následující věci:

   - Ujistěte se, že jste na svých virtuálních počítačích nainstalovali agenta Microsoft Endpoint Configuration Manager.
   - Ujistěte se, že je verze služby Microsoft Endpoint Configuration Manager aspoň na úrovni větve 1906. Nejlepších výsledků dosáhnete, když použijete větev úrovně 1910 nebo vyšší.

## <a name="configure-the-software-update-point"></a>Konfigurace bodu aktualizace softwaru

Pokud chcete dostávat aktualizace pro Windows 10 Enterprise multi-session, je potřeba povolit Windows Server, verze 1903 a novější jako produkt v rámci služby Microsoft Endpoint Configuration Manager. Toto nastavení produktu platí také v případě, že k nasazení aktualizací do systémů používáte službu Windows Server Update Service.

Příjem aktualizací:

1. Otevřete Microsoft Endpoint Configuration Manager a vyberte **lokality**.
2. Vyberte možnost **Konfigurovat součásti webu**.
3. V rozevírací nabídce vyberte možnost **bod aktualizace softwaru** .
4. Vyberte kartu **Products** (Produkty).
5. Zaškrtněte políčko, které uvádí **Windows Server verze 1903 a novější**.
6. Přejít na přehled softwarových **knihoven**  >  **Overview**  >  **aktualizace softwaru**  >  **všechny aktualizace softwaru** a vyberte možnost **synchronizovat aktualizace softwaru**.
7. Zkontrolujte soubor souboru wsyncmgr. log v **programu Program Files**  >  **Microsoft Configuration Manager**  >  **protokoly** , abyste se ujistili, že byly vaše změny uloženy. Synchronizace aktualizací může trvat několik minut.

## <a name="create-a-query-based-collection"></a>Vytvořit kolekci založenou na dotazech

Chcete-li vytvořit kolekci virtuálních počítačů s Windows 10 Enterprise s více relacemi, je možné použít kolekci založenou na dotazech k identifikaci konkrétní skladové položky operačního systému.

Postup vytvoření kolekce:

1. Vyberte **prostředky a dodržování předpisů**.
2. Přejděte na **Přehled**  >  **kolekce zařízení** a klikněte pravým tlačítkem na **kolekce zařízení** a v rozevírací nabídce vyberte **vytvořit kolekci zařízení** .
3. Na kartě **Obecné** v nabídce, která se otevře, zadejte název, který popisuje vaši kolekci, do pole **název** . V poli **Komentář** můžete zadat další informace popisující, co je kolekce. V části **omezit kolekci**definujte, které počítače do dotazu kolekce zahrnete.
4. Na kartě **pravidla členství** přidejte pravidlo pro dotaz výběrem možnosti **Přidat pravidlo**a výběrem možnosti **pravidlo dotazu**.
5. V části **Vlastnosti dotazového pravidla**zadejte název pravidla a pak definujte parametry pravidla výběrem možnosti **Upravit příkaz dotazu**.
6. Vyberte **Zobrazit příkaz dotazu**.
7. V příkazu zadejte následující řetězec:

    ```syntax
    select
    SMS_R_SYSTEM.ResourceID,SMS_R_SYSTEM.ResourceType,SMS_R_SYSTEM.Name,SMS_R_SYSTEM.SMSUniqueIdentifier,SMS_R_SYSTEM.ResourceDomainORWorkgroup,SMS_R_SYSTEM.Client
    from SMS_R_System inner join SMS_G_System_OPERATING_SYSTEM on
    SMS_G_System_OPERATING_SYSTEM.ResourceId = SMS_R_System.ResourceId where
    SMS_G_System_OPERATING_SYSTEM.OperatingSystemSKU = 175
    ```

8. Vyberte **OK** a vytvořte kolekci.
9. Pokud chcete zjistit, jestli jste úspěšně vytvořili kolekci, vyhledejte v části Přehled **prostředků a dodržování předpisů**  >  **Overview**  >  **kolekce zařízení**.
