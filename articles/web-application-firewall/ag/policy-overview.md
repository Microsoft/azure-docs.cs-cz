---
title: Přehled zásad firewallu webových aplikací Azure (WAF)
description: Tento článek je přehledem globálních zásad firewallu webových aplikací (WAF), pro jednotlivé lokality a pro identifikátor URI.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: d90c887c2e93b44a8dd13dab0b4f03665ea335c3
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960955"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Přehled zásad firewallu webových aplikací Azure (WAF)

Zásady firewallu webových aplikací obsahují všechna nastavení a konfigurace WAF. Patří sem vyloučení, vlastní pravidla, spravovaná pravidla atd. Tyto zásady jsou pak přidruženy k aplikační bráně (globálním), naslouchacímu procesu (na pracovišti) nebo pravidlu založenému na cestě (pro identifikátor URI), aby se mohly projevit.

> [!NOTE]
> Brány firewall webových aplikací Azure (WAF) pro jednotlivé lokality a zásady pro identifikátor URI jsou v Public Preview.
> 
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, mohou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Počet zásad, které můžete vytvořit, není nijak omezený. Když vytváříte zásadu, musí být přidružená k aplikační bráně, aby se projevila. Dá se přidružit k libovolné kombinaci aplikačních bran, posluchačů a pravidel založených na cestách.

## <a name="global-waf-policy"></a>Globální zásady WAF

Když přidružíte zásadu WAF globálně, každá lokalita za vaší Application Gateway WAF je chráněná stejnými spravovanými pravidly, vlastními pravidly, vyloučeními a dalšími nakonfigurovanými nastaveními.

Pokud chcete použít jednu zásadu pro všechny lokality, můžete k ní přidružit zásadu služby Application Gateway. Další informace najdete v tématu [Vytvoření zásad firewallu webových aplikací pro Application Gateway](create-waf-policy-ag.md) vytvoření a použití zásad WAF pomocí Azure Portal. 

## <a name="per-site-waf-policy"></a>Zásady WAF podle webu

Pomocí zásad pro WAF pro jednotlivé lokality můžete chránit několik lokalit s různými požadavky na zabezpečení za jednou WAF pomocí zásad pro jednotlivé lokality. Pokud máte například pět webů za vaší WAF, můžete mít pět samostatných zásad WAF (jeden pro každý naslouchací proces) pro přizpůsobení vyloučení, vlastních pravidel, spravovaných sad pravidel a všech dalších nastavení WAF pro jednotlivé lokality.

Řekněme, že se na aplikační bráně nastavila globální zásada. Pak použijete pro naslouchací proces v této aplikační bráně jinou zásadu. Zásady naslouchacího procesu se teď projeví jenom u tohoto naslouchacího procesu. Globální zásady služby Application Gateway se stále vztahují na všechny ostatní naslouchací procesy a pravidla na základě cest, ke kterým nejsou přiřazená konkrétní zásada.

## <a name="per-uri-policy"></a>Zásady pro identifikátor URI

Pro ještě více přizpůsobení na úrovni identifikátoru URI můžete přidružit zásadu WAF k pravidlu na základě cesty. Pokud některé stránky v rámci jedné lokality vyžadují různé zásady, můžete v zásadách WAF provádět změny, které mají vliv jenom na daný identifikátor URI. To se může vztahovat na platební nebo přihlašovací stránku nebo na jakékoli jiné identifikátory URI, které potřebují ještě konkrétnější zásady WAF, než ostatní weby za vaší WAF.

Podobně jako u zásad WAF pro jednotlivé lokality potlačuje konkrétnější zásady méně konkrétní. To znamená, že zásady vázané na identifikátor URI na mapě cest URL přepíšou všechny zásady pro lokalitu a globální WAF nad ní.

## <a name="example"></a>Příklad:

Řekněme, že máte tři lokality: contoso.com, fabrikam.com a adatum.com vše za stejnou aplikační bránou. Chcete WAF použít na všechny tři lokality, ale potřebujete přičíst zabezpečení pomocí adatum.com, protože to znamená, že zákazníci navštěvují, procházejí a kupují produkty.

V případě potřeby můžete použít globální zásady na WAF, s některými základními nastaveními, vyloučeními nebo vlastními pravidly, pokud je to potřeba k zastavení některých falešně pozitivních dat. V takovém případě není nutné mít spuštěná globální pravidla pro vkládání SQL, protože fabrikam.com a contoso.com jsou statické stránky bez back-endu SQL. Tato pravidla pak můžete zakázat v globálních zásadách.

Tato globální zásada je vhodná pro contoso.com a fabrikam.com, ale musíte být opatrní s adatum.com, kde se zpracovávají informace o přihlášení a platby. U naslouchacího procesu adatum můžete použít zásadu pro jednotlivé weby a ponechat pravidla SQL spuštěná. Taky předpokládáme, že soubor cookie blokuje nějaký provoz, takže můžete vytvořit vyloučení pro tento soubor cookie, abyste zastavili falešně pozitivní výsledky. 

Adatum.com/payments identifikátor URI je tam, kde je potřeba být opatrní. Proto u tohoto identifikátoru URI použijte jinou zásadu a nechte všechna pravidla povolená a také odeberte všechna vyloučení.

V tomto příkladu máte globální zásadu, která se vztahuje na dvě lokality. Máte zásady pro jednotlivé lokality, které platí pro jednu lokalitu, a pak zásady pro jednotlivé identifikátory URI, které platí pro jedno konkrétní pravidlo založené na cestě. V tomto příkladu najdete odkaz (vložení odkazu sem), jak vytvořit zásady pro jednotlivé lokality a identifikátory URI pro odpovídající prostředí PowerShell.

## <a name="existing-waf-configurations"></a>Existující konfigurace WAF

Všechna nová nastavení WAF firewallu webových aplikací (vlastní pravidla, konfigurace sady spravovaných pravidel, vyloučení atd.) existují v zásadách WAF. Pokud máte existující WAF, tato nastavení můžou pořád existovat v konfiguraci WAF. Další informace o přechodu na nové zásady WAF najdete v tématu (odkaz: migrace WAF config do zásady WAF). 


## <a name="next-steps"></a>Další kroky

Pomocí Azure PowerShell vytvořit zásady pro jednotlivé lokality a identifikátory URI.
