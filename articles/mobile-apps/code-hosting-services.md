---
title: Hostování zdrojového kódu mobilní aplikace v cloudu pomocí GitHubu a Azure DevOps
description: Přečtěte si o službách pro hostování kódu mobilní aplikace v cloudu pomocí služeb Microsoftu.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: f0af2ee5e62bbdbfb5d18cffc3e3ed62edec81f3
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795624"
---
# <a name="cloud-hosted-source-code-management-services"></a>Služby správy zdrojového kódu hostované v cloudu
Pokud máte vývojové týmy s více členy týmu pracujícími na stejném základu kódu, je nutné najít vhodné místo pro hostování vašeho kódu. Ukládání dat v cloudu a použití centralizovaného úložiště umožňuje všem uživatelům nahrávat, upravovat a spravovat soubory s kódem, pracovat s ostatními vývojáři na projektech a poskytovat kód tak, aby byl snadno přístupný bez ohledu na to, kde jste. připojení k Internetu.

Hostování cloudu je mnohem jednodušší než u místních možností, protože vyžaduje nižší hardwarovou konfiguraci a umožňuje organizacím dokončit proces implementace agilním způsobem.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Výhody hostování zdrojového kódu v cloudu
- **Centralizované cloudové úložiště** pro prohlížení a správu dat odkudkoli.
- Umožňuje **lepší spolupráci a čisticí kód** v rámci týmů, aby bylo možné sledovat kód a spravovat projekty, aby se zajistilo průběžné doručování skvělého softwaru.
- **Zařiďte** se a usnadňujte **přispívat** k vašim projektům.
- **Rychlejší cyklus vydávání verzí** , protože váš tým může pracovat rychleji a **snadno přispívat** k vašim projektům.
- **Snižte náklady** , aniž byste se museli starat o údržbu vlastního hardwaru, serverů, sítě VPN a tak dále.

K hostování aplikačních dat v cloudu použijte následující služby.

## <a name="github"></a>GitHubu
[GitHub](https://github.com/) je open source hostovaná služba, která je hostitelem projektů zdrojového kódu v různých programovacích jazycích a sleduje různé změny provedené u každé iterace.

**Klíčové funkce**
- **Hostování kódu** se všemi vaším kódem na jednom místě. U privátních, veřejných nebo Open sourceů jsou všechna úložiště vybavená nástroji, které vám pomůžou s hostováním, verzí a kódem verze.
- Bezproblémová **Revize kódu** a integrované nástroje pro kontrolu usnadňují kontrolu kódu důležitou část všech týmů.
    - Chránit větve, navrhovat změny a vyžádat revize.
    - Podívejte se na rozdíl a komentář v kontextu a získejte jasné názory.
- Zajistěte si špičku a zůstaňte v souladu s **nástroji pro správu projektu**na GitHubu.
    - Podívejte se na velký obrázek projektu.
    - Panely **úloh** , které jsou přímo vedle vašeho kódu v rámci GitHubu.
    - **Přetáhněte karty** , abyste mohli přiřadit problémy nebo žádosti o přijetí změn členům týmu.
    - **Milníky** k uspořádání a sledování průběhu.
    - **Poznámky** k zachycení nápadů, které mohou být užitečné, ale nepatří do konkrétního problému nebo žádosti o přijetí změn.
- Umožňuje snadno vyhledat a zvolit správné nástroje pro lepší komunikaci a automatizaci práce zakoupením aplikací z **[webu GitHub Marketplace](https://github.com/marketplace)** .
- **Správa týmu** umožňuje snadnou správu a rozšiřování týmů, **uživatelských rolí** , které usnadňují organizování týmů a přístupových oprávnění, nástrojů **diskuzních vláken** , které vám pomůžou s tématem zaměřeném na témata a týmy a **pokyny** pro rychlé nastavení. Noví členové týmu jsou v rámci účtu.
- Procházejte **nejoblíbenějšími** projekty a sledujte je.
- **Síť a Naučte** se od ostatních v oboru.

**Odkazy**
- [GitHub](https://github.com/)
- [Příručky pro GitHub](https://guides.github.com/)
- [Komunitní fórum GitHubu](https://github.community/)
- [Tržiště GitHubu](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) podporuje [Azure Repos](https://azure.microsoft.com/services/devops/repos/) a [Správa verzí Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako možnosti správy zdrojového kódu. Má neomezená bezplatná privátní úložiště s revizemi kódu, pokročilou správou souborů, vyhledáváním kódu a zásadami větví, abyste zajistili vysoce kvalitní kód. Azure Repos je skvělé pro malé projekty i pro velké organizace, které potřebují nativní podporu AAD a rozšířené zásady.
    
**Klíčové funkce**
- **Neomezené úložiště zdrojového kódu Git hostovaného v cloudu** pro veřejná a privátní úložiště
    - Podpora pro všechny klienty Git.
    - Webové háky a integrace rozhraní API.
- Aktivovat **Další Build** od žádosti o získání dat
    - Spolupracujte a Sestavujte lepší kód pomocí diskusí s vlákny a průběžné integrace pro každou změnu.
    - Nastavte průběžnou integraci / průběžné doručování (CI/CD), které zajistí automatickou aktivaci sestavení, testů a nasazení při každém dokončení žádosti o přijetí změn pomocí Azure Pipelines nebo vašich nástrojů.
    - Chraňte své kvality kódu pomocí zásad větví.
- **Centralizovaná správa verzí pomocí Správa verzí Team Foundation**, včetně revize kódu.
- **Připojte se ke svému kódu** pomocí Xcode, zatmění, IntelliJ, Android Studio, Visual studia, Visual Studio Code a dalších.
- **Výkonné vyhledávání sémantických kódů**.
- **Enterprise je připravený** jako nativní integrace s Azure Active Directory (AD), což zjednodušuje proces správy přístupu k úložištím kódu.
- **Zajištění kvality kódu** pomocí zásad větví, jako je například minimální počet revizí kódu, vyžadování úspěšných sestavení a vynucování strategií sloučení Git.
- **Připojte své oblíbené vývojové prostředí** pro přístup k úložištím a správě práce.

**Odkazy**
- [Začínáme s Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentace k Azure Repos](/azure/devops/repos/?view=azure-devops)