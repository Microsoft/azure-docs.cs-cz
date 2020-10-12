---
title: Publikování aplikací v samostatných sítích prostřednictvím skupin konektorů – Azure AD
description: Popisuje, jak vytvářet a spravovat skupiny konektorů v Azure Proxy aplikací služby AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3e99ca57957e1975313fed8609533f4a65b102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764719"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů

Zákazníci využívají proxy aplikace Azure AD pro další a více scénářů a aplikací. Takže jsme proxy aplikací udělali ještě pružně tím, že povolíte další topologie. Můžete vytvořit skupiny konektorů proxy aplikací, abyste mohli přiřadit konkrétní konektory, které budou obsluhovat konkrétní aplikace. Tato funkce poskytuje větší kontrolu a možnosti pro optimalizaci nasazení proxy aplikací.

Každý konektor proxy aplikace je přiřazený ke skupině konektorů. Všechny konektory, které patří do stejné skupiny konektorů, fungují jako samostatná jednotka pro vysokou dostupnost a vyrovnávání zatížení. Všechny konektory patří do skupiny konektorů. Pokud nevytvoříte skupiny, všechny vaše konektory jsou ve výchozí skupině. Správce může vytvořit nové skupiny a přiřadit k nim konektory v Azure Portal.

Všechny aplikace jsou přiřazeny ke skupině konektorů. Pokud nevytvoříte skupiny, všechny vaše aplikace se přiřadí do výchozí skupiny. Pokud však vaše konektory uspořádáte do skupin, můžete nastavit, aby každá aplikace pracovala s konkrétní skupinou konektorů. V takovém případě pouze konektory v této skupině budou aplikaci používat na vyžádání. Tato funkce je užitečná v případě, že jsou vaše aplikace hostovány v různých umístěních. Můžete vytvořit skupiny konektorů na základě umístění, aby byly aplikace vždy obsluhovány konektory, které jsou fyzicky blízko.

> [!TIP]
> Pokud máte velké nasazení aplikačního proxy serveru, nepřiřazujte žádné aplikace do výchozí skupiny konektorů. Nové konektory tak nebudou dostávat žádný živý provoz, dokud je nepřiřazujete do skupiny aktivních konektorů. Tato konfigurace také umožňuje vkládat konektory do režimu nečinnosti tak, že je přesunete zpátky do výchozí skupiny, takže můžete provádět údržbu bez dopadu na uživatele.

## <a name="prerequisites"></a>Požadavky

Chcete-li seskupit konektory, musíte se ujistit, že jste [nainstalovali více konektorů](application-proxy-add-on-premises-application.md). Při instalaci nového konektoru se automaticky připojí **výchozí** skupina konektorů.

## <a name="create-connector-groups"></a>Vytvoření skupin konektorů

Pomocí těchto kroků můžete vytvořit tolik skupin konektorů, kolik chcete.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory**  >  **Enterprise applications**  >  **aplikační proxy aplikace**pro podnikové aplikace.
1. Vyberte možnost **Nová skupina konektorů**. Zobrazí se okno Nová skupina konektoru.

   ![Zobrazuje obrazovku pro výběr nové skupiny konektorů.](./media/application-proxy-connector-groups/new-group.png)

1. Zadejte název nové skupiny konektorů a pak pomocí rozevírací nabídky vyberte, které konektory patří do této skupiny.
1. Vyberte **Uložit**.

## <a name="assign-applications-to-your-connector-groups"></a>Přiřazení aplikací ke skupinám konektorů

Tyto kroky použijte pro každou aplikaci, kterou jste publikovali pomocí proxy aplikace. Aplikaci můžete přiřadit do skupiny konektorů při jejím prvním publikování, nebo můžete pomocí těchto kroků změnit přiřazení kdykoli, když chcete.

1. Z řídicího panelu pro správu vašeho adresáře vyberte **podnikové aplikace**  >  **všechny aplikace** > aplikaci, kterou chcete přiřadit ke skupině konektorů > **proxy aplikací**.
1. Pomocí rozevírací nabídky **Skupina konektoru** vyberte skupinu, kterou chcete použít v aplikaci.
1. Vyberte **Uložit** a použijte změnu.

## <a name="use-cases-for-connector-groups"></a>Případy použití pro skupiny konektorů

Skupiny konektorů jsou užitečné pro různé scénáře, včetně:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Weby s více propojenými datacentry

Mnoho organizací má řadu vzájemně propojených datových center. V takovém případě chcete udržovat co nejvíc přenosů dat v rámci datového centra, protože propojení mezi datovými centry jsou nákladná a citlivá. V každém datovém centru můžete nasazovat konektory, které budou obsluhovat jenom aplikace, které se nacházejí v datacentru. Tento přístup minimalizuje odkazy mezi datovými centru a poskytuje uživatelům zcela transparentní prostředí.

### <a name="applications-installed-on-isolated-networks"></a>Aplikace nainstalované v izolovaných sítích

Aplikace je možné hostovat v sítích, které nejsou součástí hlavní podnikové sítě. Skupiny konektorů můžete použít k instalaci vyhrazených konektorů v izolovaných sítích a také k izolaci aplikací v síti. K tomu obvykle dochází, když dodavatel třetí strany udržuje konkrétní aplikaci pro vaši organizaci.

Skupiny konektorů umožňují instalovat vyhrazené konektory pro tyto sítě, které publikují pouze konkrétní aplikace, což usnadňuje a zvyšuje zabezpečení pro správu aplikací na základě externích výrobců.

### <a name="applications-installed-on-iaas"></a>Aplikace nainstalované v IaaS

Pro aplikace nainstalované v IaaS pro cloudový přístup poskytují skupiny konektorů společnou službu pro zabezpečení přístupu ke všem aplikacím. Skupiny konektorů nevytvářejí další závislosti v podnikové síti nebo fragmentují prostředí aplikace. Konektory lze nainstalovat do každého cloudového datového centra a obsluhovat pouze aplikace, které se nacházejí v dané síti. Můžete nainstalovat několik konektorů, abyste dosáhli vysoké dostupnosti.

Vezměte v úvahu jako příklad organizaci, která má několik virtuálních počítačů připojených ke své vlastní hostované virtuální síti IaaS. Aby zaměstnanci mohli tyto aplikace používat, jsou tyto privátní sítě připojené k podnikové síti pomocí sítě VPN typu Site-to-site. To poskytuje dobré prostředí pro zaměstnance, kteří se nacházejí v místním prostředí. Ale nemusí být ideální pro vzdálené zaměstnance, protože k směrování přístupu vyžaduje další místní infrastrukturu, jak vidíte v následujícím diagramu:

![Diagram, který ilustruje síť Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Pomocí skupin konektorů Azure Proxy aplikací služby AD můžete povolit společné služby pro zabezpečení přístupu ke všem aplikacím bez vytváření další závislosti v podnikové síti:

![Dodavatelé více cloudů Azure AD IaaS](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Více doménových struktur – různé skupiny konektorů pro každou doménovou strukturu

Většina zákazníků, kteří nasadili proxy aplikací, používá své možnosti jednotného přihlašování (SSO) pomocí omezeného delegování protokolu Kerberos (KCD). Aby to bylo možné, musí být počítače konektoru připojeny k doméně, která může delegovat uživatele směrem k aplikaci. KCD podporuje možnosti mezi doménovými strukturami. Pro společnosti, které mají odlišná prostředí s více doménovými strukturami bez vztahu důvěryhodnosti, ale nelze použít jeden konektor pro všechny doménové struktury. 

V takovém případě je možné nasazovat konkrétní konektory na doménovou strukturu a nastavit na obsluhované aplikace, které budou sloužit pouze uživatelům této konkrétní doménové struktury. Každá skupina konektorů představuje jinou doménovou strukturu. I když je tenant a většina prostředí sjednocená pro všechny doménové struktury, můžou být uživatelé přiřazeni k aplikacím doménové struktury pomocí skupin Azure AD.

### <a name="disaster-recovery-sites"></a>Lokality pro zotavení po havárii

Existují dva různé přístupy, které můžete provést s lokalitou zotavení po havárii (DR), v závislosti na tom, jak se vaše weby implementují:

* Pokud je vaše lokalita DR vytvořená v režimu aktivní – aktivní, kde je přesně stejná jako hlavní lokalita a má stejné nastavení sítě a AD, můžete vytvořit konektory na webu DR ve stejné skupině konektorů jako hlavní lokalita. Díky tomu může Azure AD zjišťovat převzetí služeb při selhání.
* Pokud je vaše lokalita DR oddělená od hlavní lokality, můžete vytvořit jinou skupinu konektorů v lokalitě DR a buď jednu z nich má zálohovací aplikace, nebo 2) ručně přesměrovat existující aplikaci do skupiny konektorů DR podle potřeby.

### <a name="serve-multiple-companies-from-a-single-tenant"></a>Obsluha více společností z jednoho tenanta

Existuje mnoho různých způsobů implementace modelu, ve kterém jeden poskytovatel služeb nasazuje a udržuje služby související s Azure AD pro více společností. Skupiny konektorů pomůžou správci oddělit konektory a aplikace do různých skupin. Jedním ze způsobů, který je vhodný pro malé společnosti, je mít jednoho tenanta Azure AD, zatímco různé společnosti mají vlastní název domény a sítě. To platí také pro M&scénáře a situace, kdy jedno oddělení IT obsluhuje několik společností pro zákonné nebo obchodní důvody.

## <a name="sample-configurations"></a>Ukázkové konfigurace

Mezi příklady, které můžete implementovat, patří následující skupiny konektorů.

### <a name="default-configuration--no-use-for-connector-groups"></a>Výchozí konfigurace – žádné použití pro skupiny konektorů

Pokud nepoužíváte skupiny konektorů, vaše konfigurace by vypadala takto:

![Příklad služby Azure AD – žádné skupiny konektorů](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)

Tato konfigurace je dostačující pro malá nasazení a testy. Bude taky fungovat i v případě, že vaše organizace má plochou síťovou topologii.

### <a name="default-configuration-and-an-isolated-network"></a>Výchozí konfigurace a izolovaná síť

Tato konfigurace je vývojem výchozího rozhraní, ve kterém existuje konkrétní aplikace, která běží v izolované síti, například IaaS Virtual Network:

![Příklad služby Azure AD žádné skupiny konektorů a izolované sítě](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)

### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Doporučená konfigurace – několik specifických skupin a výchozí skupina pro nečinné

Doporučená konfigurace velkých a složitých organizací má mít výchozí skupinu konektorů, která neobsluhuje žádné aplikace a používá se pro nečinné nebo nově nainstalované konektory. Všechny aplikace jsou obsluhovány pomocí přizpůsobených skupin konektorů. To umožňuje veškerou složitost výše popsaných scénářů.

V následujícím příkladu má společnost dvě datová centra, a a B se dvěma konektory, které obsluhují jednotlivé lokality. Každá lokalita má různé aplikace, které jsou na ní spuštěné.

![Příklad společnosti se dvěma datacentry a 2 konektory](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)

## <a name="next-steps"></a>Další kroky

* [Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
* [Povolení jednoduchého přihlášení](what-is-single-sign-on.md)
