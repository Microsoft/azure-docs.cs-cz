---
title: Přehled zásad brány Azure Web Application Firewall (WAF)
description: Tento článek je přehled webových aplikací firewall (WAF) globální, na webu a na-URI zásady.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060266"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Přehled zásad brány Azure Web Application Firewall (WAF)

Zásady brány firewall webových aplikací obsahují všechna nastavení a konfigurace WAF. To zahrnuje vyloučení, vlastní pravidla, spravovaná pravidla a tak dále. Tyto zásady jsou pak přidruženy k aplikační bráně (globální), naslouchací proces (na webu) nebo pravidlo založené na cestě (per-URI), aby se projevily.

> [!NOTE]
> Azure Web Application Firewall (WAF) pro web a identifikátor URI jsou ve verzi Public Preview.
> 
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, mohou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Počet zásad, které můžete vytvořit, není nijak omezen. Při vytváření zásady musí být přidružena k bráně aplikace, aby se projevila. Může být přidružena k libovolné kombinaci aplikačních bran, naslouchacích procesů a pravidel založených na cestě.

## <a name="global-waf-policy"></a>Globální politika WAF

Když globálně přidružíte zásady WAF, každý web za vaším wafem aplikační brány je chráněn stejnými spravovanými pravidly, vlastními pravidly, vyloučeními a dalšími nakonfigurovanými nastaveními.

Pokud chcete, aby se jedna zásada vztahovala na všechny weby, můžete zásadu přidružit k bráně aplikace. Další informace najdete [v tématu Vytvoření zásad brány firewall webových aplikací pro aplikační bránu](create-waf-policy-ag.md) k vytvoření a použití zásad WAF pomocí portálu Azure. 

## <a name="per-site-waf-policy"></a>Zásady WAF pro pracoviště

Pomocí zásad WAF pro jednotlivé lokality můžete chránit více webů s různými potřebami zabezpečení za jedním souborem WAF pomocí zásad pro jednotlivé lokality. Pokud je například za vaším wafem pět webů, můžete mít pět samostatných zásad WAF (jeden pro každý naslouchací proces) pro přizpůsobení vyloučení, vlastních pravidel, sad spravovaných pravidel a všech ostatních nastavení WAF pro každý web.

Řekněme, že vaše aplikační brána má globální zásady, které se na něj vztahují. Potom použijete jinou zásadu na schytavku na této aplikační bráně. Zásady posluchače nyní vstoupí v platnost pouze pro tento naslouchací proces. Globální zásady aplikační brány se stále vztahují na všechny ostatní naslouchací procesy a pravidla založená na cestě, která nemají přiřazenu konkrétní zásadu.

## <a name="per-uri-policy"></a>Zásady pro identifikátor Y URI

Chcete-li ještě více vlastního nastavení až na úroveň URI, můžete přidružit zásadu WAF k pravidlu založenému na cestě. Pokud existují určité stránky v rámci jednoho webu, které vyžadují různé zásady, můžete provést změny zásad WAF, které ovlivňují pouze daný identifikátor URI. To se může vztahovat na platební stránku nebo přihlašovací stránku nebo na jakékoli jiné identifikátory URI, které potřebují ještě konkrétnější zásady WAF než ostatní weby za vaším wafem.

Stejně jako u zásad WAF pro jednotlivé lokality mají konkrétnější zásady přednost před méně specifickými zásadami. To znamená, že zásady pro každou identifikátor URI na mapě cesty URL přepíší všechny zásady waf na webu nebo globální zásady WAF nad ní.

## <a name="example"></a>Příklad

Řekněme, že máte tři weby: contoso.com, fabrikam.com a adatum.com všechny za stejnou aplikační bránou. Chcete použít waf na všechny tři weby, ale potřebujete větší zabezpečení s adatum.com protože to je místo, kde zákazníci navštěvují, procházejí a nakupují produkty.

Můžete použít globální zásady waf, s některými základními nastaveními, vyloučení nebo vlastní pravidla v případě potřeby zastavit některé falešně pozitivních z blokování provozu. V tomto případě není nutné mít globální sql injection pravidla spuštěna, protože fabrikam.com a contoso.com jsou statické stránky bez back-endu SQL. Takže můžete zakázat tato pravidla v globální politice.

Tato globální politika je vhodná pro contoso.com a fabrikam.com, ale musíte být opatrnější při adatum.com, kde jsou zpracovávány přihlašovací informace a platby. Můžete použít zásady pro web adatum naslouchací proces a ponechat pravidla SQL spuštěna. Předpokládejme také, že je cookie blokuje nějaký provoz, takže můžete vytvořit vyloučení pro tento soubor cookie zastavit falešně pozitivní. 

Identifikátor URI adatum.com/payments je místo, kde je třeba být opatrný. Proto použijte jiné zásady na tomto identifikátoru URI a ponechte všechna pravidla povolena a také odeberte všechna vyloučení.

V tomto příkladu máte globální zásady, které platí pro dva weby. Máte zásady pro jednotlivé lokality, které se vztahují na jeden web, a pak zásadu uri, která se vztahuje na jedno konkrétní pravidlo založené na cestě. Viz (vložit odkaz zde, pokud existuje) jak vytvořit zásady pro web a na IDENTIFIKÁTOR URI pro odpovídající Prostředí PowerShell v tomto příkladu.

## <a name="existing-waf-configurations"></a>Existující konfigurace WAF

Všechna nová nastavení WAF brány FIREWALL pro webové aplikace (vlastní pravidla, konfigurace sad spravovaných pravidel, vyloučení a tak dále). Pokud máte existující WAF, tato nastavení mohou stále existovat v konfiguraci WAF. Další informace o přechodu na novou zásadu WAF [získáte migrací konfigurace WAF do zásad waf](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Další kroky

Vytvořte zásady pro web a identifikátor URI pomocí Azure PowerShellu.
