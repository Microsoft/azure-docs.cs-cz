---
title: Začínáme s Azure Active Directory | Microsoft Docs
description: Získání licence, přidejte název domény, vytvořte vlastní přihlašovací stránku a přidejte hesla pomocí samoobslužné služby resetovat v adresáři Azure Active Directory
keywords: ''
author: curtand
manager: mtillman
ms.author: curtand
ms.reviewer: jsnow
ms.date: 11/14/2017
ms.topic: article
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
services: active-directory
ms.custom: it-pro
ms.openlocfilehash: bf7647d8489a102175c4e9de799793e6550495e7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34714404"
---
# <a name="get-started-with-azure-ad"></a>Začínáme s Azure AD
Moderní identity managementu vyžaduje proporcionálních a konzistentní reliablity k zajištění dostupnosti probíhá aplikací a služeb pouze ověřené uživatele. Pro potřeby správy identity uživatelů, musí oddělení IT dostat adekvátní podporu způsob, jak poskytnout přístup k schválené, veřejné software jako služba (SaaS) aplikace, způsob, jak hostitele řádku interní obchodní aplikace a i prostředky pro zlepšení místní vývoj aplikací a využití. Všechny tyto požadavky nasměrujte potřeba řešení pro správu cloudových identit.      

Azure Active Directory (Azure AD) je Microsoft je více klientů, cloudu na základě adresáře a identity management service. Azure AD kombinuje základní adresářové služby, pokročilé identity zásad správného řízení a správa přístupu aplikací. Návrh víceklientské, zeměpisné polohy, vysokou dostupnost služby Azure AD znamená, že můžete spoléhat na pro vaše nejdůležitější obchodní potřeby.

Azure AD zahrnuje úplná sada funkcí správy identit, včetně možnosti synchronizace informací o prostředku v místě, přizpůsobitelné firemní branding, Správa licencí jednoduché a správu hesla pomocí samoobslužné služby i. Tyto možnosti pro snadné nasazení můžete začít používat Azure AD zabezpečené cloudové aplikace, zjednodušit procesy IT, snížit náklady a zajistit, že byly splněny dodržování podnikových předpisů cíle.

![Azure AD ](./media/get-started-azure-ad/Azure_Active_Directory.png)

Zbývající část tohoto článku se dozvíte, jak začít pracovat s Azure AD. 

## <a name="sign-up-for-azure-active-directory-premium"></a>Registrace služby Azure Active Directory Premium
K [Začínáme s Azure Active Directory (Azure AD) Premium](active-directory-get-started-premium.md), můžete nákupu licencí a přidružit je ve vašem předplatném Azure. Pokud vytvoříte nové předplatné služby Azure, musíte se také k aktivaci licenčního plánu a přístup k službě Azure AD. 

Active Directory Premium si můžete registrovat několika způsoby: 

- Použitím předplatného Azure nebo Office 365.
- Použitím licenčního plánu Enterprise Mobility + Security.
- Použitím plánu multilicenčního programu společnosti Microsoft.

### <a name="verification-step"></a>Krok ověření
Po aktivaci předplatného, ujistěte se, že vám umožní se přihlásit ke službě.

## <a name="add-a-custom-domain-name"></a>Přidání vlastního názvu domény
Každý adresář Azure AD se dodává s počáteční název domény ve formě *domainname*. onmicrosoft.com. Název domény nelze změnit ani odstranit, ale můžete také [přidat název vaší firemní domény do Azure AD](add-custom-domain.md). Například vaše organizace nemá pravděpodobně jiné názvy domén, umožňuje udělat firmy a uživatele, kteří se přihlašují pomocí firemního názvu domény. Přidání vlastních názvů domén do Azure AD umožňuje přiřazovat uživatelská jména v adresáři, které jsou pro uživatele, jako například 'alice@contoso.com. " místo 'alice@.onmicrosoft.com'. Proces je jednoduchý:

1. Přidání vlastního názvu domény do adresáře
2. Přidání položky DNS pro název domény u registrátora názvu domény
3. Ověření vlastního názvu domény v Azure AD

### <a name="verification-step"></a>Krok ověření
Po přidání vlastní doménu, zkontrolujte, zda má **ověřeno** stav se zobrazí na **vlastní názvy domén** okno portálu Azure AD.

## <a name="add-company-branding-to-your-sign-in-page"></a>Přidání firemního brandingu na přihlašovací stránce 
Mnoho společností chce předcházet zmatení uživatele a upřednostňuje jednotný vzhled všech webů a služeb, které spravují. Azure Active Directory (Azure AD) tuto možnost nabízí a umožňuje [přizpůsobení vzhledu stránku přihlášení s svoje firemní logo a vlastní barevná schémata](customize-branding.md). Přihlašovací stránka je stránka, která se zobrazí při přihlášení k Office 365 nebo jiné webové aplikace, které používají Azure AD jako jejich zprostředkovatele identity. Budete používat tuto stránku k zadání pověření.

### <a name="verification-step"></a>Krok ověření
Přihlaste se k portálu Azure a zkontrolujte, jestli vaše upravené stránky pro přihlášení a další jazyková nastavení správně nakonfigurovaný. 

## <a name="add-new-users"></a>Přidat nové uživatele
Můžete [přidání nových uživatelů do vaší organizace Azure AD](add-users-azure-active-directory.md) jednoho současně pomocí portálu Azure nebo synchronizací dat prostředků systému Windows Server AD místně. Můžete přidat cloudových uživatelů přímo z portálu Azure AD nebo synchronizovat informace o uživateli na místě.

Pokud chcete povolit místní synchronizaci identit s Azure AD, je nutné na serveru ve vaší organizaci nainstalovat a nakonfigurovat službu [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect). Tato aplikace se stará o synchronizaci uživatelů a skupin mezi vaším existujícím zdrojem identit a vaším tenantem Azure AD.

### <a name="verification-step"></a>Krok ověření
Po vytvoření nebo synchronizaci nových uživatelů, ujistěte se, že jsou viditelné ve službě Azure AD.

## <a name="assign-licenses"></a>Přiřazení licencí
I když získání předplatného je vše, budete muset nakonfigurovat placené funkce, je nutné stále [přiřadit uživatelské licence](license-users-groups.md) placené funkce Azure AD Premium. Každý uživatel, který mají mít přístup k nebo který je spravován prostřednictvím Azure AD placené funkce musí být přiřazena licence. Přiřazení licence je mapování mezi uživateli a zakoupené služby, jako je například Azure AD Premium, Basic nebo Enterprise Mobility + Security.

Přiřazení na základě skupiny licencí můžete použít k nastavení pravidel, jako v následujících příkladech:

- Všichni uživatelé v adresáři automaticky získat licenci
- Všichni uživatelé s názvem příslušné úlohy využít licenci
- Rozhodnutí ohledně jiným správcům v organizaci můžete delegovat (pomocí samoobslužných skupin)

### <a name="verification-step"></a>Krok ověření
Zkontrolujte přiřazené a dostupných licencí v rámci **Azure Active Directory** > **licence** > **všechny produkty**.

## <a name="configure-self-service-password-reset"></a>Konfigurovat samoobslužné resetování hesla
[Samoobslužného obnovení hesla (SSPR)](authentication/quickstart-sspr.md) nabízí jednoduchý znamená pro správce IT, aby mohli uživatelé resetovat nebo odemknout jejich hesla nebo účty. Tento systém zahrnuje vytváření podrobných sestav, pomocí kterých můžete sledovat, kdy uživatelé systém používají, spolu s oznámeními, která upozorňují na zneužití.

### <a name="verification-step"></a>Krok ověření
Zkontrolujte povolené vlastnosti SSPR pod **Azure Active Directory** > **resetování hesla** zajistit správné uživatele a skupiny přiřazení byly provedeny. 


## <a name="next-steps"></a>Další postup
[Stránky služby Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

[Azure Active Directory informace o stránce s cenami.](https://azure.microsoft.com/pricing/details/active-directory/)