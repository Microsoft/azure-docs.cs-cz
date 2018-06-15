---
title: Publikování aplikací na samostatných sítí a umístění v skupiny konektoru Proxy aplikace Azure AD | Microsoft Docs
description: Popisuje postup vytvoření a Správa skupin konektorů v Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: H1Hack27Feb2017; it-pro
ms.openlocfilehash: 50f17d7b833874cd15ddc9f2e4f93fa7af1700a2
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162005"
---
# <a name="publish-applications-on-separate-networks-and-locations-using-connector-groups"></a>Publikování aplikací na samostatných sítí a umístění pomocí konektoru skupin

Zákazníci využívat proxy aplikace služby Azure AD pro více scénářů a aplikace. Proto jsme provedli jsme Proxy aplikace i flexibilnější povolením více topologií. Můžete vytvořit skupiny konektoru Proxy aplikace, takže můžete přiřadit konkrétní konektory poskytovat konkrétní aplikace. Tato funkce poskytuje další kontrolu a způsoby, jak optimalizovat vašemu nasazení proxy serveru aplikace. 

Každý konektor Proxy aplikace je přiřadit ke skupině konektor. Všechny konektory, které patří do stejné skupiny konektor fungovat jako samostatná jednotka pro vysokou dostupnost a vyrovnávání zatížení. Všechny konektory patří do skupiny konektor. Pokud nevytvoříte skupin, všechny konektory jsou ve výchozí skupině. Správce můžete vytvořit nové skupiny a k nim přiřadíte konektorů na portálu Azure. 

Všechny aplikace jsou přiřazeny ke skupině konektor. Pokud nevytvoříte skupiny, jsou přiřazeny všechny aplikace na výchozí skupinu. Ale pokud je vaše konektory uspořádat do skupin, můžete nastavit každá aplikace pro práci s konkrétní konektor skupiny. V takovém případě pouze konektory v dané skupině poskytovat aplikaci na vyžádání. Tato funkce je užitečná, pokud vaše aplikace, které jsou hostované v různých umístěních. Konektor skupiny založené na umístění, můžete vytvořit tak, aby aplikace jsou vždy obsloužených konektory, které jsou fyzicky blízko je.

>[!TIP] 
>Pokud máte velké nasazení služby Proxy aplikace, nepřiřazovat všechny aplikace na výchozí skupinu konektor. Tímto způsobem nové konektory nejsou přijímat přenosy za provozu, dokud je nezařadíte do skupiny konektor služby active. Tato konfigurace umožňuje put konektory v režimu nečinnosti jejich přesunutím zpět na výchozí skupinu tak, aby bylo možné provádět údržby bez dopadu na vaši uživatelé.

## <a name="prerequisites"></a>Požadavky
K seskupení vaší konektory, budete muset nezapomeňte [nainstalovat více konektorů](application-proxy-enable.md). Pokud instalujete nový konektor, automaticky připojí **výchozí** konektor skupiny.

## <a name="create-connector-groups"></a>Vytvořit konektor skupiny
Pomocí těchto kroků můžete vytvořit libovolný počet skupin konektor. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte **Azure Active Directory** > **podnikové aplikace, které** > **proxy aplikace**.
2. Vyberte **nové skupiny konektor**. Otevře se okno nové skupiny konektor.

   ![Vyberte novou skupinu konektoru](./media/application-proxy-connector-groups/new-group.png)

3. Zadejte název nové skupiny konektor, potom použijte rozevírací nabídky vyberte konektory, které patří do této skupiny.
4. Vyberte **Uložit**.

## <a name="assign-applications-to-your-connector-groups"></a>Přiřadit aplikací do skupin konektoru
Tyto kroky použijte pro každou aplikaci, kterou jste publikovali pomocí Proxy aplikace. Když publikujete nejprve nebo těchto kroků můžete vždy, když chcete změnit přiřazení můžete přiřadit aplikaci pro skupinu konektor.   

1. Řídicí panel správy pro váš adresář, vyberte **podnikové aplikace, které** > **všechny aplikace** > aplikace, které chcete přiřadit ke skupině konektoru > **Proxy aplikace**.
2. Použít **konektor skupiny** rozevírací nabídce vyberte skupinu, kterou má aplikace použít.
3. Vyberte **Uložit** na použití změny.

## <a name="use-cases-for-connector-groups"></a>Případy použití pro konektor skupiny 

Konektor skupiny jsou užitečné pro různé scénáře, včetně:

### <a name="sites-with-multiple-interconnected-datacenters"></a>Weby s několik datových center vzájemně propojena

Mnoho organizací má číslo vzájemně propojena datových center. V takovém případě chcete zachovat tolik provoz v rámci datového centra nejdříve, protože datacenter křížové odkazy jsou nákladné a pomalé. Můžete nasadit konektorů v každé datové centrum, která bude sloužit pouze aplikace, které se nacházejí v datovém centru. Tento postup minimalizuje datacenter křížové odkazy a poskytuje zcela transparentní pro vaše uživatele.

### <a name="applications-installed-on-isolated-networks"></a>Aplikace nainstalované v izolovaných sítích

Aplikace může být hostovaný v sítích, které nejsou součástí hlavní podnikové síti. Konektor skupiny můžete použít k instalaci vyhrazené konektory v izolovaných sítích také izolovat aplikace do sítě. Obvykle se to stane, když jiného dodavatele udržuje konkrétní aplikace pro vaši organizaci. 

Konektor skupiny umožňují instalace vyhrazené konektorů pro tyto sítě, které publikovat jenom konkrétní aplikace, což jednodušší a bezpečnější využívající správu aplikací na jiných dodavatelů.

### <a name="applications-installed-on-iaas"></a>Aplikace nainstalované na IaaS 

Pro aplikace nainstalované v IaaS pro přístup do cloudu konektor skupiny poskytují běžné služby zabezpečený přístup ke všem aplikacím. Konektor skupiny nemáte vytvoření další závislosti na vaší podnikové síti nebo fragmentu aplikační prostředí. Konektory lze nainstalovat na každé cloudové datacentrum a sloužit pouze aplikace, které se nacházejí v této síti. Můžete nainstalovat několik konektorů k dosažení vysoké dostupnosti.

Trvat jako příklad, že v organizaci, která má několik virtuálních počítačů, které jsou připojené k vlastní IaaS hostované virtuální sítě. Umožnit zaměstnancům používat tyto aplikace jsou těchto privátních sítí připojené k podnikové síti prostřednictvím sítě site-to-site VPN. To umožňuje kvalitní zaměstnanci, kteří se nacházejí na místních. Ale nemusí být ideální pro vzdálení zaměstnanci, protože vyžaduje další místní infrastrukturu ke směrování přístupu, jak je vidět na obrázku níže:

![AzureAD Iaas sítě](./media/application-proxy-connector-groups/application-proxy-iaas-network.png)
  
Pomocí skupin Azure AD Application Proxy connector můžete povolit běžné služby zabezpečený přístup ke všem aplikacím, aniž by byla vytvořena další závislosti ve vaší podnikové síti:

![Dodavatelé cloudu Iaas více AzureAD](./media/application-proxy-connector-groups/application-proxy-multiple-cloud-vendors.png)

### <a name="multi-forest--different-connector-groups-for-each-forest"></a>Více doménovými strukturami – konektor jiné skupiny pro každou doménovou strukturu

Většina zákazníků, kteří mají nasazenou Proxy aplikace používají její jednotného přihlašování (SSO) možnosti provedením Kerberos vynuceným delegování použitím (KCD). Jak toho docílit, třeba počítače konektoru být připojené k doméně, která může delegovat uživatele k aplikaci. Použitím KCD podporuje funkce mezi doménovými strukturami. Ale pro společnosti, kteří mají odlišné prostředí více doménovými strukturami bez vztahu důvěryhodnosti mezi nimi, jeden konektor nelze použít pro všechny doménové struktury. 

V takovém případě konkrétní konektory můžete nasazení pro každou doménovou strukturu a nastavit k obsluze aplikace, které byly publikované poskytovat jenom uživatelé této konkrétní doménové struktury. Každá skupina konektor představuje jiné doménové struktuře. Při klienta a většina prostředí je jednotná pro všechny doménové struktury, uživatelé se dají přiřadit k jejich aplikacím doménové struktury pomocí skupin Azure AD.
 
### <a name="disaster-recovery-sites"></a>Weby pro zotavení po havárii

Existují dva různé přístupy, které můžete provést s lokalitou zotavení po havárii, v závislosti na tom, jak jsou implementované vaše lokality:

* Pokud váš web zotavení po Havárii je integrovaný v režimu aktivní aktivní, kde je úplně stejně jako hlavní lokalitu a má stejné sítě a nastavení služby AD, můžete vytvořit konektory na webu zotavení po Havárii ve stejné skupině konektor jako hlavní lokalitu. To umožňuje službě Azure AD můžete zjistit převzetí služeb při selhání.
* Pokud váš webový server zotavení po Havárii je oddělené od hlavního webu, můžete vytvořit skupinu jiný konektor v lokalitě zotavení po Havárii a 1) byl zálohování aplikace nebo 2) ručně přesměrovat stávající aplikace do skupiny konektor zotavení po Havárii, podle potřeby.
 
### <a name="serve-multiple-companies-from-a-single-tenant"></a>Sloužit více společností z jednoho klienta

Jsou služby pro více společností související s mnoha různými způsoby implementovat modelu, ve kterém se u jednoho poskytovatele nasadí a udržuje Azure AD. Konektor skupiny pomáhají správce oddělit konektorů a aplikací do různých skupin. Jedním ze způsobů, který je vhodný pro malé firmy, je tak, aby měl jeden Azure AD klientů, zatímco jiné společnosti mají své vlastní název domény a sítě. To platí také pro M & A scénáře a situace, kdy jednu oblast IT slouží několik společností charakterem předpisů nebo obchodních důvodů. 

## <a name="sample-configurations"></a>Ukázka konfigurace

Některé příklady, které můžete implementovat, zahrnují následující skupiny konektor.
 
### <a name="default-configuration--no-use-for-connector-groups"></a>Výchozí konfigurace – bez použití pro konektor skupiny

Pokud nepoužijete konektor skupiny, bude vaše konfigurace vypadat takto:

![AzureAD žádné skupiny pro konektor](./media/application-proxy-connector-groups/application-proxy-sample-config-1.png)
 
Tato konfigurace je dostatečný pro malá nasazení a testy. Bude také vhodný, pokud má vaše organizace ploché síťové topologie.
 
### <a name="default-configuration-and-an-isolated-network"></a>Výchozí konfigurace a izolovanou síť

Tato konfigurace je výchozí nastavení, ve kterém je konkrétní aplikaci, která běží v izolované síti jako jsou například IaaS virtuální sítě: 

![AzureAD žádné skupiny pro konektor](./media/application-proxy-connector-groups/application-proxy-sample-config-2.png)
 
### <a name="recommended-configuration--several-specific-groups-and-a-default-group-for-idle"></a>Doporučená konfigurace – několik konkrétních skupin a výchozí skupiny pro nečinnosti

Doporučenou konfiguraci pro komplexní i velké organizace se se do výchozí skupiny konektor jako skupinu, která nebude poskytovat všechny aplikace a používá se pro nově nainstalovanou nebo nečinné konektory. Všechny aplikace se zpracovávají pomocí vlastní konektor skupin. To umožňuje všechny složitosti scénářů popsané výše.

V následujícím příkladu má společnost zavedeny dvou Datacenter, A a B, s dva konektory, které slouží každou lokalitu. Každá lokalita má jiné aplikace, které běží na něm. 

![AzureAD žádné skupiny pro konektor](./media/application-proxy-connector-groups/application-proxy-sample-config-3.png)
 
## <a name="next-steps"></a>Další postup

* [Pochopení konektory proxy aplikace služby Azure AD](application-proxy-connectors.md)
* [Povolení jednoduchého přihlášení](application-proxy-single-sign-on.md)


