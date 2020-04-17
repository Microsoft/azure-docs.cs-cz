---
title: Verze analýzy bezpečnostního kódu společnosti Microsoft
description: Tento článek popisuje nadcházející verze rozšíření Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 04/14/2020
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: a07ce7437d664baca0cfdc310dbc2631f41fdbcc
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81462032"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Verze a plán analýzy bezpečnostního kódu společnosti Microsoft

Tým pro analýzu bezpečnostního kódu společnosti Microsoft ve spolupráci s podporou vývojářů s potěšením oznamuje nejnovější a nadcházející vylepšení našeho rozšíření MSCA. Viz plán níže.

![Vydání](./media/security-code-analysis-releases/releases.png)

## <a name="credential-scanner-v20-released-on-april-1-2020"></a>Credential Scanner v2.0: Vydáno 1.

### <a name="innovations--improvements"></a>Inovace & zlepšení

- **Základní motor**

   - Průměrný výkon upgrade 25% s téměř lineární doby chodu
   - Kontextové/důkazní vyhledávání a hodnocení pro zvýšení přesnosti
   - Vylepšení obecných detekcí hesel a logiky párování pro zřejmé zástupné symboly (například fakePassword)

- **Pokrytí** - Podpora pro 25 + tajné typy, včetně následujících top požadované:

   - Přístupové heslo certifikátu účtu Fabric
   - Tajný klíč klienta/klíč rozhraní API
   - Hlavička autorizace protokolu HTTP
   - Amazon S3 Klient tajný přístupový klíč
   - Token klientského přístupu služby Azure Active Directory
   - Klíč hlavního serveru funkcí a rozhraní API Azure
   - Přístupový klíč Power BI
   - Vzor hesla šablony Správce prostředků Azure

- **Výstupy**

   - Podpora formátů výstupních souborů souborů SARIF 2.1 a CSV

## <a name="binskim-v160-to-be-released-on-april-2020"></a>BinSkim v1.6.0: Bude vydánv dubnu 2020

### <a name="improvements"></a>Zlepšení

- FUNKCE: Aktualizace konečného SARIF v2 (verze 2.1.16). To umožňuje výsledky ukládání do mezipaměti při předávání --hashes na příkazovém řádku, významné zlepšení výkonu při rekurzivně analýze adresářů s více kopiemi cílů prohledávání.
- OPRAVA CHYBY: Oprava překlepu v BA2021. DoNotMarkWritableSectionsAsExecutable výstup.
- VÝKON: Eliminujte načítání PDB pro všechny nesmíšené režimy pro spravované sestavení, včetně knihovny IL (předem kompilované) binárních souborů.
- FALSE NEGATIVE FIX: Ověřte, zda pdb umístěný vedle binárního souboru skutečně odpovídá binárnímu souboru v analýze
- FUNKCE: Zadejte argument --local-symbol-directories pro určení dalších (místních, nesymbol-serverových) umístění vyhledávání PDB
- FALSE POSITIVE FIX: Přeskočit analýzu řízenou PDB pro generovaný nativní bootin exe jádra .NET (což není uživatelsky kontrolovatelný kód).

## <a name="whats-next-in-fy20"></a>Co bude dál v FY20?

- Nástroj java bezpečnostní analýzy
- Nástroj analýza zabezpečení Pythonu
- ES Lint nahradit TS Lint pro TypeScript a JavaScript

## <a name="next-steps"></a>Další kroky

Pokyny k připojení a instalaci analýzy kódu zabezpečení společnosti Microsoft naleznete [v příručce Onboarding and Installation Guide](security-code-analysis-onboard.md).

Máte-li další dotazy týkající se rozšíření a nabízených nástrojů, podívejte se na naši [stránku faq](security-code-analysis-faq.md).
