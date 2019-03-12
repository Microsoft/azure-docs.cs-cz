---
title: Interaktivní funkce ověřovací testování v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit interaktivní funkce ověřovacích testů pro službu Azure Stack s ověřováním jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d3db8ea8639f73f3522ddaa358195e7c9ef2f9a9
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57765998"
---
# <a name="interactive-feature-verification-testing"></a>Ověřovací testování interaktivní funkce  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Testovací rozhraní interaktivní funkce ověřování můžete vyžádat testy pro váš systém. Pokud budete požadovat testu, společnost Microsoft používá rozhraní připravit testy, které vyžadují ruční zásah interaktivní. Microsoft můžete použít rozhraní pro zřetězení několik samostatných automatizované testy.

Tento článek popisuje jednoduchý scénář ruční. Test ověří, Výměna disku ve službě Azure Stack. Rozhraní framework shromažďuje diagnostické protokoly v každém kroku. Problémy můžete ladit, protože je najít. Rozhraní také umožňuje sdílet protokoly vytvořené pomocí jiných nástrojů nebo procesy a umožňuje poskytnout zpětnou vazbu na scénáři.

> [!Important]  
> Tento článek odkazuje na postup provedení identifikace disku. To je jednoduše ukázku, protože žádné výsledky shromážděné z pracovního postupu průchodu testů nelze použít pro ověření nového řešení.

## <a name="overview-of-interactive-testing"></a>Přehled interaktivní testování

Test pro náhradní disk je běžný scénář. V tomto příkladu test má pět kroků:

1. Vytvořte nový **průchodu testů** pracovního postupu.
2. Vyberte **disku identifikace Test**.
3. Provedení ručního kroku po zobrazení výzvy.
4. Podívat se na výsledek tohoto scénáře.
5. Výsledek testu poslat Microsoftu.

## <a name="create-a-new-test-pass"></a>Vytvořit nový průchodu testu

Pokud nemáte existujícího testu předat k dispozici, postupujte podle pokynů pro [plánování testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Testovací plán

1. Vyberte **disku identifikace Test**.

    > [!Note]  
    > Zajištění testu vylepšili se zvýšit číslo verze testu. Nejvyšší verze se musí použít značka Pokud Microsoft označuje jinak.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Zadejte uživatelské jméno správce domény a heslo tak, že vyberete **upravit**.

1. Vyberte odpovídajícím testovacím spuštění agenta/DVM ke spuštění testu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Vyberte **odeslat** spuštění testu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Přístup k UI pro interaktivní test z agenta vybrána v předchozím kroku.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Postupujte podle **dokumentaci** a **ověření** odkazy na pokyny od Microsoftu k provedení tohoto scénáře.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Postupujte podle pokynů pro spuštění precheck skriptu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Po úspěšném dokončení skriptu precheck spustit ruční scénář (Výměna disku), v souladu **dokumentaci** a **ověření** odkazy ze **informace**kartu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Dialogové okno nezavírejte, při provádění ruční scénář.

1. Po dokončení provádění ruční scénář, postupujte podle pokynů pro spuštění pozálohovacího skriptu vrácení.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Při úspěšném dokončení ruční scénáře (Výměna disku), vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Pokud okno zavřete, test se zastaví dříve, než se to dělá.

1. Poskytnout zpětnou vazbu pro testovací prostředí. Tyto otázky vám pomůže Microsoft posouzení jakosti úspěch rychlost a vydání scénáře.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Připojte všechny soubory protokolů, které chcete odesílat společnosti Microsoft.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Přijměte smlouvu EULA odesláním zpětné vazby.

1. Vyberte **odeslat** na výsledky odeslat do Microsoftu.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
