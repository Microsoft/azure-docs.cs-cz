---
title: Ověřování funkčnosti AppSource Dynamics 365 finance a operací v Azure Marketplace.
description: Postup při navýšení platnosti nabídky finance a operací Dynamics 365 v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: emuench
ms.author: navits
ms.date: 07/17/2020
ms.openlocfilehash: 9be90cdac742a581c6346f923f44e769c8a70f76
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613625"
---
# <a name="appsource-dynamics-365-finance-and-operations-functional-validation"></a>AppSource Dynamics 365 finance a ověřování funkčnosti operací

Aby bylo možné dokončit první publikování v [partnerském centru](https://partner.microsoft.com/dashboard/home), nabídky pro finance a operace Dynamics 365 vyžadují dvě funkční ověřování:

- Nahrajte demonstrační video prostředí Dynamics 365, které zobrazuje základní funkce.
- Prezentovat snímky obrazovky, které ukazují prostředí [služby pro životní cyklus](https://lcs.dynamics.com/) (LCS) řešení.

> [!NOTE]
> Publikování následujících opětovných potvrzení nevyžaduje ukázku. Další informace najdete v [dokumentu zásady AppSource](/legal/marketplace/certification-policies#1440-dynamics-365-finance-ops).

## <a name="how-to-validate"></a>Jak ověřit

Pro ověření funkčnosti existují dvě možnosti:

- Po dobu trvání konferenčního hovoru s námi v pracovní době v Tichomoří (běžný čas), která předvádí a zaznamenává prostředí [LCS](https://lcs.dynamics.com/) a řešení
- V partnerském centru klikněte na [Přehled komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)  >   a na kartě doplňkového obsahu nabídky nahrajte ukázkovou adresu URL s ukázkovým videem a obrazovky LCS.

Certifikační tým Microsoftu zkontroluje video a soubory a pak buď schválí řešení, nebo pošle e-maily o dalších krocích.

### <a name="option-1-30-minute-conference-call"></a>Možnost 1:30-minuta konferenční hovor

Chcete-li naplánovat konečný hovor na revizi, kontaktujte [appsourceCRM@microsoft.com](mailto:appsourceCRM@microsoft.com) s názvem vaší nabídky a několika potenciálními časovými sloty mezi 8 dop. a 5 hodin tichomořského času.

### <a name="option-2-upload-a-demo-video-and-lcs-screenshots"></a>Možnost 2: nahrání ukázkového videa a snímků obrazovky LCS

1. Zaznamenejte si video a nahrajte adresu na hostující web podle vašeho výběru. Postupujte podle těchto pokynů:

    - Zobrazitelné týmem Microsoftu pro certifikace.
    - Doba kratší než 20 minut.
    - Zahrnuje až tři hlavní funkce v prostředí Dynamics 365.

    > [!NOTE]
    > Je přijatelné použít stávající marketingové video, pokud splňuje pokyny.

2. Proveďte následující snímky obrazovky prostředí [LCS](https://lcs.dynamics.com/) , které odpovídají nabídce nebo řešení, které chcete publikovat. Aby mohl Certifikační tým číst text, musí být dostatečně jasné. Uložte snímky obrazovky jako soubory JPG. Můžete poskytnout [appSourceCRM@microsoft.com](mailto:appSourceCRM@microsoft.com) oprávnění k vašemu prostředí LCS, abychom mohli místo poskytování snímků obrazovky ověřit nastavení.

    1. Přejít na   >  knihovnu projektů LCS **Business Process Modeler**  >  . Pořídit snímky obrazovky všech kroků procesu. Zahrňte **diagramy** a **revidované** sloupce, jak je znázorněno zde:

       :::image type="content" source="media/dynamics-365-finance-operations/project-library.png" alt-text="Zobrazuje okno knihovny projektu.":::

      2. Přejít na   >    >  **balíček testovacího řešení** správy řešení LCS Pořídit snímky obrazovky, které obsahují přehled balíčku a obsah uvedený v těchto příkladech:

    | Pole | Image |
    | --- | --- |
    | Přehled balíčku | [![Snímek obrazovky se zobrazeným oknem Přehled balíčku](media/dynamics-365-finance-operations/package-overview-45.png)](media/dynamics-365-finance-operations/package-overview.png#lightbox) |
    | <ul><li>Schvalovatelé řešení</li></ul> | [![Obrazovka s přehledem balíčku](media/dynamics-365-finance-operations/solution-approvers-45.png)](media/dynamics-365-finance-operations/solution-approvers.png#lightbox) |
    | Obsah balíčku<ul><li>Modelování</li><li>Balíček s nasazením softwaru</li></ul> | [![Obrazovka obsahu balíčku 1](media/dynamics-365-finance-operations/package-contents-1-45.png)](media/dynamics-365-finance-operations/package-contents-1.png#lightbox) |
    | <ul><li>Konfigurace GER</li><li>Zálohování databáze</li></ul><br>V **konfiguračním oddílu GER** se nevyžadují artefakty. | [![Obrazovka obsahu balíčku 2](media/dynamics-365-finance-operations/package-contents-2-45.png)](media/dynamics-365-finance-operations/package-contents-2.png#lightbox) |
    | <ul><li>Model sestavy Power BI</li><li>Artefakt BPM</li></ul><br>Artefakty nejsou v oddílu **Power BI** požadovány. | [![Obrazovka obsahu balíčku – tři](media/dynamics-365-finance-operations/package-contents-3-45.png)](media/dynamics-365-finance-operations/package-contents-3.png#lightbox) |
    | <ul><li>Zpracovat balíček dat</li><li>Licenční smlouva k řešení a zásady ochrany osobních údajů</li></ul><br>Části **GER Configuration** and **Power BI model sestavy** jsou volitelné, aby se zahrnuly do nabídek finance a operace. | [![Obrazovka obsahu balíčku – čtyři](media/dynamics-365-finance-operations/package-contents-4-45.png)](media/dynamics-365-finance-operations/package-contents-4.png#lightbox) |

    Další informace o jednotlivých oddílech portálu LCS najdete v [uživatelské příručce LCS](/dynamics365/fin-ops-core/dev-itpro/lifecycle-services/lcs-user-guide).

3. Odeslat do partnerského centra

    1. Vytvořte textový dokument, který obsahuje ukázkovou adresu a snímky obrazovky, nebo uložte snímky obrazovky jako samostatné soubory JPG.
    2. Přidejte text a jakékoli soubory JPG do souboru. zip na [komerčním webu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) v partnerském centru na kartě s **doplňkovým obsahem** nabídky finance a operace.

    [![Zobrazuje okno knihovny projektu.](media/dynamics-365-finance-operations/supplemental-content.png)](media/dynamics-365-finance-operations/supplemental-content.png#lightbox)

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření nabídky najdete v tématu: [Vytvoření nabídky Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md).