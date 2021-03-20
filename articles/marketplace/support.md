---
title: Podpora programu pro komerční tržiště v partnerském centru
description: Seznamte se s možnostmi podpory pro komerční program Marketplace v partnerském centru, včetně postupu při zapracování žádosti o podporu.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: a1726b29c153bf680d29fe821ac34aa958064335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879237"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Podpora programu komerčního tržiště v partnerském centru

Společnost Microsoft poskytuje podporu pro širokou škálu produktů a služeb. Hledání správného týmu podpory je důležité pro zajištění vhodné a včasné reakce. Vezměte v úvahu následující scénáře, které vám pomůžou směrovat dotaz na příslušný tým:

- Pokud jste Vydavatel a máte dotaz od zákazníka, požádejte zákazníka, aby požádal o podporu pomocí odkazů na podporu v [Azure Portal](https://portal.azure.com/).
- Pokud jste Vydavatel a zjistili jste problémy se zabezpečením aplikace spuštěné v Azure, přečtěte si téma [Jak protokolovat lístek podpory událostí zabezpečení](../security/fundamentals/event-support-ticket.md). Vydavatelé musí ohlásit podezřelé události zabezpečení, včetně bezpečnostních incidentů a ohrožení zabezpečení jejich Azure Marketplace softwaru a služeb, a to nejdřívějším příležitostí.
- Pokud jste Vydavatel a máte dotaz týkající se vaší aplikace nebo služby, Projděte si následující možnosti podpory.

## <a name="get-help-or-open-a-support-ticket"></a>Získání pomoci nebo otevření lístku podpory

1. Přihlaste se pomocí svého pracovního účtu. Pokud jste to ještě neudělali, budete muset [vytvořit účet partnerského centra](partner-center-portal/create-account.md).

1. V nabídce v pravém horním rohu stránky vyberte ikonu **Podpora** . Podokno **pomoc a podpora** se zobrazí na pravé straně stránky.

1. Pokud chcete získat nápovědu k komerčnímu tržišti, vyberte **komerční tržiště**.

   ![Rozevírací nabídka podpora](./media/support/commercial-marketplace-support-pane.png)

1. V poli **Shrnutí problému** zadejte stručný popis problému.

1. V poli **typ problému** proveďte jednu z následujících akcí:

    - **Možnost 1**: zadejte klíčová slova, jako je: Marketplace, aplikace Azure, nabídka SaaS, Správa účtů, Správa zájemců, problém nasazení, výběr nebo spoluprodejní nabídka. Pak z doporučeného seznamu vyberte typ problému.

    - **Možnost 2**: vyberte **Procházet témata** v seznamu **kategorie** a pak vyberte **komerční tržiště**. Pak vyberte příslušné **téma** a dílčí **téma**.

1. Po nalezení vybraného tématu vyberte možnost **Zkontrolovat řešení**.

    ![Další krok](./media/support/next-step.png)

Zobrazí se následující možnosti:

- Pokud chcete vybrat jiné téma, klikněte na **vybrat jiný problém**.
- Pokud chcete problém vyřešit, Projděte si doporučené kroky a dokumenty, pokud jsou k dispozici.

    ![Doporučená řešení](./media/support/recommended-solutions.png)

Pokud nemůžete najít odpověď v svépomocné nápovědě, vyberte možnost **poskytnout podrobnosti o problému**. Dokončete všechna povinná pole pro urychlení procesu řešení a pak vyberte **Odeslat**.

>[!Note]
>Pokud jste se přihlásili k partnerskému centru, budete se možná muset přihlásit, než budete moct vytvořit lístek.

## <a name="track-your-existing-support-requests"></a>Sledování stávajících žádostí o podporu

Pokud chcete zkontrolovat otevřené a uzavřené lístky, vyberte v levé navigační nabídce možnost Podpora **komerčního tržiště**  >  .

## <a name="record-issue-details-with-a-har-file"></a>Záznam detailů problému se souborem HAR

Chcete-li pomoci s tím, že agenti budou moci vyřešit potíže, zvažte připojení souboru HAR (HTTP Archive Format) k lístku podpory. Soubory HAR jsou protokoly síťových požadavků ve webovém prohlížeči.

> [!WARNING]
> Soubory HAR můžou nahrávat citlivá data týkající se vašeho účtu partnerského centra.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge a Google Chrome

Vygenerování souboru HAR pomocí **Microsoft Edge** nebo **Google Chrome**:

1. Přejít na webovou stránku, na které dochází k problému.
2. V pravém horním rohu okna vyberte ikonu se třemi tečkami a pak **Další nástroje** pro  >  **vývojáře**. Klávesu F12 můžete stisknout jako zástupce.
3. V podokně nástroje pro vývojáře vyberte kartu **síť** .
4. Vyberte **Zastavit záznam síťového protokolu** a **zrušte zaškrtnutí** a odeberte existující protokoly. Ikona záznamu změní šedou.

    ![Jak odebrat existující protokoly v Microsoft Edge nebo Google Chrome](media/support/chromium-stop-clear-session.png)

5. Vyberte **záznam síťového protokolu** pro spuštění záznamu. Při zahájení záznamu se ikona záznamu změní na červenou.

    ![Jak začít nahrávat v Microsoft Edge nebo Google Chrome](media/support/chromium-start-session.png)

6. Reprodukování problému, který chcete vyřešit.
7. Po reprodukování problému vyberte **Zastavit záznam síťového protokolu**.
8. Vyberte **exportovat Har**, označený ikonou šipky dolů a uložte soubor.

    ![Export souboru HAR na Microsoft Edge nebo Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Vygenerování souboru HAR pomocí **Mozilla Firefox**:

1. Přejít na webovou stránku, na které dochází k problému.
1. V pravém horním rohu okna vyberte ikonu se třemi tečkami a pak nástroje **Web Developer**  >  **Toggle Tools**. Klávesu F12 můžete stisknout jako zástupce.
1. Vyberte kartu **síť** a pak výběrem **Zrušit** odeberte existující protokoly.

    ![Postup odebrání stávajících protokolů v Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reprodukování problému, který chcete vyřešit.
1. Po reprodukování problému vyberte **Har export/import**  >  **Uložit vše jako Har**.

    ![Export souboru HAR v Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Postup vygenerování souboru HAR pomocí **prohlížeče Safari**:

1. Povolte vývojářské nástroje v Safari: vyberte předvolby **Safari**  >  . Přejděte na kartu **Upřesnit** a pak na **panelu nabídek vyberte možnost Zobrazit nabídku vývoje**.
1. Přejít na webovou stránku, na které dochází k problému.
1. Vyberte **vývoj** a pak vyberte **Zobrazit web Inspector**.
1. Vyberte kartu **síť** a pak výběrem **položky vymazat síťové položky** odeberte existující protokoly.

    ![Jak odebrat existující protokoly v Safari](media/support/safari-clear-session.png)

1. Reprodukování problému, který chcete vyřešit.
1. Po reprodukování problému vyberte **exportovat** a uložte soubor.

    ![Export souboru HAR v Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](partner-center-portal/update-existing-offer.md)