---
title: Publikování aplikací v samostatných sítích prostřednictvím skupin konektorů – Azure AD
description: Popisuje, jak vytvořit a spravovat skupiny konektorů v proxy aplikací Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fa1de0a0e3bb91480212381e07b17875bf0bf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275567"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů

Zákazníci využívají proxy aplikací Azure AD pro další a další scénáře a aplikace. Takže jsme udělali App Proxy ještě flexibilnější tím, že umožňuje více topologie. Můžete vytvořit skupiny konektorů proxy aplikace, takže můžete přiřadit určité konektory pro konkrétní aplikace. Tato funkce poskytuje větší kontrolu a způsoby optimalizace nasazení proxy aplikací.

Každý konektor proxy aplikace je přiřazen ke skupině konektorů. Všechny konektory, které patří do stejné skupiny konektorů, fungují jako samostatná jednotka pro vysokou dostupnost a vyrovnávání zatížení. Všechny konektory patří do skupiny konektorů. Pokud skupiny nevytvoříte, budou všechny konektory ve výchozí skupině. Správce může vytvářet nové skupiny a přiřazovat jim konektory na webu Azure Portal.

Všechny aplikace jsou přiřazeny ke skupině konektorů. Pokud skupiny nevytvoříte, budou všechny aplikace přiřazeny k výchozí skupině. Ale pokud uspořádáte konektory do skupin, můžete nastavit každou aplikaci pro práci s konkrétní skupinou konektorů. V tomto případě pouze konektory v této skupině slouží aplikace na vyžádání. Tato funkce je užitečná, pokud jsou aplikace hostovány na různých místech. Skupiny spojnic můžete vytvořit na základě umístění, aby aplikace byly vždy obsluhovány konektory, které jsou fyzicky blízko k nim.

> [!TIP]
> Pokud máte velké nasazení proxy aplikací, nepřiřazuj te žádné aplikace k výchozí skupině konektorů. Tímto způsobem nové konektory neobdrží žádné živé přenosy, dokud je nepřiřadíte k aktivní skupině konektorů. Tato konfigurace také umožňuje umístit konektory do nečinného režimu přesunutím zpět do výchozí skupiny, takže můžete provádět údržbu bez dopadu na uživatele.

## <a name="prerequisites"></a>Požadavky

Chcete-li seskupit konektory, musíte se ujistit, že jste [nainstalovali více konektorů](application-proxy-add-on-premises-application.md). Když nainstalujete nový konektor, automaticky se připojí k **výchozí** skupině konektorů.

## <a name="create-connector-groups"></a>Vytvoření skupin konektorů

Pomocí těchto kroků vytvořte libovolný počet skupin spojnic.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte azure **active directory** > **podnikové aplikace** > **proxy aplikace**.
1. Vyberte **nová skupina spojnic**. Zobrazí se okno Nová skupina konektorů.

   ![Zobrazí obrazovku pro výběr nové skupiny konektorů.](./media/application-proxy-connector-groups/new-group.png)

1. Pojmenujte novou skupinu konektorů a pomocí rozevírací nabídky vyberte, které konektory do této skupiny patří.
1. Vyberte **Uložit**.

## <a name="assign-applications-to-your-connector-groups"></a>Přiřazení aplikací ke skupinám konektorů

Tyto kroky použijte pro každou aplikaci, kterou jste publikovali s proxy aplikací. Při prvním publikování můžete přiřadit aplikaci skupině konektorů nebo můžete pomocí těchto kroků změnit přiřazení, kdykoli budete chtít.

1. Na řídicím panelu správy pro váš adresář vyberte **podnikové aplikace** > **Všechny aplikace** > aplikaci, kterou chcete přiřadit skupině konektorů > proxy **aplikace**.
1. Pomocí rozevírací nabídky **Skupina konektorů** vyberte skupinu, kterou má aplikace používat.
1. Chcete-li změnu použít, vyberte **Uložit.**

## <a name="use-cases-for-connector-groups"></a>Případy použití pro skupiny konektorů

Skupiny konektorů jsou užitečné pro různé scénáře, včetně:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Lokality s více propojenými datovými centry

Mnoho organizací má několik propojených datových center. V takovém případě chcete zachovat co nejvíce provozu v rámci datového centra, protože propojení mezi datovými centry jsou drahá a pomalá. Můžete nasadit konektory v každém datovém centru sloužit pouze aplikace, které jsou umístěny v rámci datového centra. Tento přístup minimalizuje propojení mezi datovými centry a poskytuje uživatelům zcela transparentní prostředí.

### <a name="applications-installed-on-isolated-networks"></a>Aplikace nainstalované v izolovaných sítích

Aplikace mohou být hostovány v sítích, které nejsou součástí hlavní podnikové sítě. Skupiny konektorů můžete použít k instalaci vyhrazených konektorů v izolovaných sítích a izolovat tak aplikace do sítě. K tomu obvykle dochází, když dodavatel třetí strany udržuje konkrétní aplikaci pro vaši organizaci.

Skupiny konektorů umožňují instalovat vyhrazené konektory pro sítě, které publikují pouze určité aplikace, což usnadňuje a zabezpečuje správu aplikací dodavatelům třetích stran.

### <a name="applications-installed-on-iaas"></a>Aplikace nainstalované na IaaS

Pro aplikace nainstalované na IaaS pro přístup ke cloudu, skupiny konektorů poskytují společnou službu pro zabezpečení přístupu ke všem aplikacím. Skupiny konektorů nevytvářejí další závislost na podnikové síti ani nefragmentují prostředí aplikace. Konektory lze nainstalovat do každého cloudového datového centra a obsluhovat pouze aplikace, které jsou umístěny v této síti. Můžete nainstalovat několik konektorů pro dosažení vysoké dostupnosti.

Vezměme si jako příklad organizace, která má několik virtuálních počítačů připojených k jejich vlastní virtuální síti hostované IaaS. Aby mohli zaměstnanci používat tyto aplikace, jsou tyto privátní sítě připojeny k podnikové síti pomocí sítě VPN site-to-site. To poskytuje dobré prostředí pro zaměstnance, kteří se nacházejí v místním prostředí. Ale nemusí být ideální pro vzdálené zaměstnance, protože vyžaduje další místní infrastrukturu pro směrování přístupu, jak můžete vidět v diagramu níže:

![Diagram, který znázorňuje síť Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Pomocí skupin konektoru proxy aplikací Azure AD můžete povolit společnou službu pro zabezpečení přístupu ke všem aplikacím bez vytváření dalších závislostí na podnikové síti:

![Azure AD IaaS více cloudových dodavatelů](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Vícedoménová doménová struktura – různé skupiny spojnic pro každou doménovou strukturu

Většina zákazníků, kteří nasadili proxy aplikace, používá jeho funkce jednotného přihlašování (SSO) provedením delegování s omezeným i protokolem Kerberos (KCD). K dosažení tohoto cíle je nutné připojit počítače konektoru k doméně, která může delegovat uživatele směrem k aplikaci. KCD podporuje možnosti mezi doménami. Ale pro společnosti, které mají odlišné prostředí s více doménovými strukturami bez důvěry mezi nimi, jeden konektor nelze použít pro všechny doménové struktury. 

V takovém případě lze nasadit konkrétní konektory pro jednotlivé doménové struktury a nastavit tak, aby sloužily aplikacím, které byly publikovány tak, aby sloužily pouze uživatelům této konkrétní doménové struktury. Každá skupina spojnic představuje jinou doménovou strukturu. Zatímco tenant a většina prostředí je jednotná pro všechny doménové struktury, uživatelé mohou být přiřazeny k jejich aplikacím doménové struktury pomocí skupin Azure AD.

### <a name="disaster-recovery-sites"></a>Weby zotavení po havárii

Existují dva různé přístupy, které můžete provést s webem zotavení po havárii (DR), v závislosti na tom, jak jsou vaše weby implementovány:

* Pokud je váš web zotavení po Havárii zabudován v aktivním aktivním režimu, kde je přesně jako hlavní web a má stejné nastavení sítě a služby AD, můžete vytvořit konektory na webu zotavení po Havárii ve stejné skupině konektorů jako hlavní síť. To umožňuje Azure AD ke zjištění převzetí služeb při selhání pro vás.
* Pokud je váš lokalita zotavení po Havárii oddělená od hlavní lokality, můžete v lokalitě zotavení po havárii vytvořit jinou skupinu konektorů a buď 1) mít zálohovací aplikace nebo 2) ručně přesměrovat existující aplikaci do skupiny konektorů zotavení po Havárii podle potřeby.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsluhovat více společností z jednoho klienta

Existuje mnoho různých způsobů, jak implementovat model, ve kterém jeden poskytovatel služeb nasazuje a udržuje služby související s Azure AD pro více společností. Skupiny konektorů pomáhají správci oddělit konektory a aplikace do různých skupin. Jedním ze způsobů, který je vhodný pro malé společnosti, je mít jednoho klienta Azure AD, zatímco různé společnosti mají svůj vlastní název domény a sítě. To platí i pro scénáře M&A a situace, kdy jedna divize IT obsluhuje několik společností z regulačních nebo obchodních důvodů.

## <a name="sample-configurations"></a>Ukázkové konfigurace

Některé příklady, které můžete implementovat, zahrnují následující skupiny konektorů.

### <a name="default-configuration--no-use-for-connector-groups"></a>Výchozí konfigurace – bez použití pro skupiny konektorů

Pokud nepoužíváte skupiny konektorů, bude vaše konfigurace vypadat takto:

![Příklad Azure AD Žádné skupiny konektorů](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Tato konfigurace je dostatečná pro malá nasazení a testy. Bude také fungovat dobře, pokud vaše organizace má topologii ploché sítě.

### <a name="default-configuration-and-an-isolated-network"></a>Výchozí konfigurace a izolovaná síť

Tato konfigurace je vývoj výchozí, ve kterém je konkrétní aplikace, která běží v izolované síti, jako je například virtuální síť IaaS:

![Příklad Azure AD Žádné skupiny konektorů a izolované sítě](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Doporučená konfigurace – několik specifických skupin a výchozí skupina pro nečinnosti

Doporučená konfigurace pro velké a složité organizace je mít výchozí skupinu konektorů jako skupinu, která neslouží žádné aplikace a používá se pro nečinné nebo nově nainstalované konektory. Všechny aplikace jsou obsluhovány pomocí přizpůsobených skupin konektorů. To umožňuje všechny složitosti scénářů popsaných výše.

V níže uvedeném příkladu má společnost dvě datová centra, A a B, se dvěma konektory, které obsluhují každou lokalitu. Každý web má různé aplikace, které běží na něm.

![Příklad společnosti se 2 datovými centry a 2 konektory](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Další kroky

* [Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
* [Povolení jednoduchého přihlášení](what-is-single-sign-on.md)
