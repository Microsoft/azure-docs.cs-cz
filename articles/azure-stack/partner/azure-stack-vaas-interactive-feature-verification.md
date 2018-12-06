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
ms.date: 11/19/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: dc0c6a37e11574cef857a7af773a9d90bea57357
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52972061"
---
# <a name="interactive-feature-verification-testing"></a>Ověřovací testování interaktivní funkce  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Testovací rozhraní interaktivní funkce ověřování můžete vyžádat testy pro váš systém. Pokud budete požadovat testu, společnost Microsoft používá rozhraní připravit testy, které vyžadují ruční zásah interaktivní. Microsoft můžete použít rozhraní pro zřetězení několik samostatných automatizované testy.

Tento článek popisuje jednoduchý scénář ruční. Test ověří, Výměna disku ve službě Azure Stack. Rozhraní framework shromažďuje diagnostické protokoly v každém kroku. Problémy můžete ladit, protože je najít. Rozhraní také umožňuje sdílet protokoly vytvořené pomocí jiných nástrojů nebo procesy a umožňuje poskytnout zpětnou vazbu na scénáři.

## <a name="overview-of-a-test-pass"></a>Přehled průchodu testu

Test pro náhradní disk je běžný scénář. V tomto příkladu má test sedmi kroků:

1.  Vytvořte nový **průchodu testů** pracovního postupu.
2.  Vyberte **disku identifikace Test**.
3.  Spusťte test.
4.  Zvolte akce v případě interaktivního ověřování.
5.  Podívat se na výsledek tohoto scénáře.
6.  Výsledek testu poslat Microsoftu.
7.  Zkontrolujte stav VaaS portálu.

## <a name="create-a-new-test-pass"></a>Vytvořit nový průchodu testu

1.  Přejděte [portálu Azure Stack ověření](https://www.azurestackvalidation.com) a přihlaste se.

2.  Vytvoření nového řešení nebo vyberte existující.

3.  Vyberte **Start** na **průchodu testů** dlaždici.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image1.png)

4.  Zadejte název **průchodu testů** pracovního postupu.

5.  Zadejte prostředí a společné parametry testu podle pokynů v [společných parametrů pracovních postupů pro Azure Stack ověření jako služba](azure-stack-vaas-parameters.md) článku.

6.  Diagnostika připojovací řetězec musí mít formát podle [parametry testu](azure-stack-vaas-parameters.md#test-parameters) tématu [společných parametrů pracovních postupů](azure-stack-vaas-parameters.md) článku.

7.  Zadejte požadované parametry pro test.

8.  Vyberte agenta ke spuštění interaktivních testovacího běhu.

> [!Note]  
> Uživatel s rolí správce domény a heslo musí zadat pro test ověření interaktivní funkce identifikace disku.

![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image2.png)

## <a name="select-the-test"></a>Vyberte test

1.  Vyberte **Test identifikace disku\<verze >**.

    > [!Note]  
    > Zajištění testu vylepšili se zvýšit číslo verze testu. Nejvyšší verze se musí použít značka Pokud Microsoft označuje jinak.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2.  Zadejte účet správce domény a heslo tak, že vyberete **upravit**.

3.  Vyberte odpovídajícím testovacím spuštění agenta/DVM ke spuštění testu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4.  Vyberte **odeslat** spuštění testu.

![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

## <a name="start-the-test"></a>Spustit test

Na počítači, na kterém běží VaaS agent zobrazit výzvy Test identifikace disku. To je obvykle DVM nebo Jumpbox pro instanci služby Azure Stack.

![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

## <a name="choose-the-actions"></a>Zvolte Akce

1.  Postupujte podle **dokumentaci** a **ověření** odkazy na pokyny od Microsoftu k provedení tohoto scénáře.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

2.  Vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

3.  Postupujte podle pokynů pro spuštění precheck skriptu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

4.  Po úspěšném dokončení skriptu precheck provést ruční scénář (Výměna disku), v souladu **dokumentaci** a **ověření** odkazy ze **informace**kartu.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

5.  Dialogové okno nezavírejte, při provádění ruční scénář.

6.  Po dokončení provádění ruční scénář, postupujte podle pokynů pro spuštění pozálohovacího skriptu vrácení.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

7.  Při úspěšném dokončení ruční scénáře (Výměna disku), vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

> [!Important]  
> Pokud okno zavřete, test se zastaví dříve, než se to dělá.

## <a name="check-the-status"></a>Kontrola stavu

1.  Po dokončení testu se zobrazí výzva k poskytnutí zpětné vazby.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

2.  Tyto otázky vám pomůže Microsoft posouzení jakosti úspěch rychlost a vydání scénáře.

## <a name="send-the-test-result"></a>Odeslat do výsledku testu

1.  Připojte všechny soubory protokolů, které chcete odesílat společnosti Microsoft.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

2.  Přijměte smlouvu EULA odesláním zpětné vazby.

3.  Vyberte **odeslat** na výsledky odeslat do Microsoftu.

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
