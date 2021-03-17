---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114514"
---

## <a name="using-this-example-knowledge-base"></a>V tomto příkladu znalostní báze Knowledge Base

Znalostní báze v tomto rychlém startu začíná 2 QnA páry, to je prováděno za účelem zjednodušení příkladu a použití vysoce předvídatelných identifikátorů, které je možné použít v metodě Update. přiřadíte tak následné výzvy k dotazům na nové páry. Toto bylo plánováno a implementováno v konkrétním pořadí pro účely tohoto rychlého startu.

Pokud plánujete vyvíjet znalostní bázi v průběhu času s následnými výzvami, které jsou závislé na stávajících QnAch párů, můžete zvolit tyto možnosti:
* V případě větších knowledgebases spravujte znalostní bázi v textovém editoru nebo v nástroji TSV, který podporuje automatizaci, a potom zcela nahraďte znalostní bázi aktualizací.
* V případě menších knowledgebases Spravujte všechny následné výzvy úplně na portálu QnA Maker.

Podrobnosti o dvojicích QnA použitých v tomto rychlém startu:
* Typy páru QnA – v této znalostní bázi existují 2 typy párů QnA, a to po aktualizaci: chitchat a informace specifické pro doménu. To je obvyklé, pokud je vaše znalostní báze svázána s aplikací konverzace, jako je chatovací robot.
* I když je možné odpovědi na znalostní báze filtrovat podle metadat nebo použít výzvy k následnému použití, tento rychlý Start tento kurz nezobrazuje. [Tady](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md)hledejte příklady jazyků nezávislá generateAnswer.
* Text odpovědi je Markdownu a může obsahovat [širokou škálu Markdownu](../reference-markdown-format.md) , jako jsou například obrázky (veřejně dostupné internetové image), odkazy (na veřejně dostupné adresy URL) a body odrážek. v tomto rychlém startu se tato odrůda nepoužívá.
