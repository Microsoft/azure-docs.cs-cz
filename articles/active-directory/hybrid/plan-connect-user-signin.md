---
title: 'Azure AD Connect: přihlášení uživatele | Microsoft Docs'
description: Azure AD Connect přihlášení uživatele pro vlastní nastavení.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997779"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Možnosti přihlášení uživatele Azure AD Connect
Azure Active Directory (Azure AD) Connect umožňuje vašim uživatelům přihlašovat se ke cloudovým i místním prostředkům pomocí stejných hesel. Tento článek popisuje klíčové koncepty pro každý model identity, které vám pomůžou zvolit identitu, kterou chcete použít pro přihlášení k Azure AD.

Pokud už jste obeznámeni s modelem identity Azure AD a chcete získat další informace o konkrétní metodě, přečtěte si příslušný odkaz:

* [Synchronizace hodnot hash hesel](#password-hash-synchronization) pomocí [bezproblémového jednotného přihlašování (SSO)](how-to-connect-sso.md)
* [Předávací ověřování](how-to-connect-pta.md) pomocí [bezproblémového jednotného přihlašování (SSO)](how-to-connect-sso.md)
* [Federované jednotné přihlašování (s Active Directory Federation Services (AD FS) (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federace s PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Je důležité si uvědomit, že konfigurace federace pro službu Azure AD umožňuje vytvořit vztah důvěryhodnosti mezi vaším klientem služby Azure AD a federovaným doménami. S tímto důvěryhodnými uživateli federované domény budou mít přístup k prostředkům cloudu služby Azure AD v rámci tenanta.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Výběr způsobu přihlašování uživatelů pro vaši organizaci
Prvním rozhodnutím implementace Azure AD Connect je výběr metody ověřování, které budou vaši uživatelé používat k přihlášení. Je důležité se ujistit, že zvolíte správnou metodu, která splňuje zabezpečení a pokročilé požadavky vaší organizace. Ověřování je kritické, protože ověří identity uživatele pro přístup k aplikacím a datům v cloudu. Pokud chcete zvolit správnou metodu ověřování, musíte vzít v úvahu čas, stávající infrastrukturu, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory jsou pro každou organizaci odlišné a můžou se v průběhu času měnit.

Azure AD podporuje následující metody ověřování: 

* **Cloudové ověřování** – Pokud zvolíte tuto metodu ověřování, Azure AD zpracuje proces ověřování pro přihlášení uživatele. Pomocí cloudového ověřování můžete vybrat ze dvou možností: 
   * **Synchronizace hodnot hash hesel (kosmetice)** – synchronizace hodnot hash hesel umožňuje uživatelům používat stejné uživatelské jméno a heslo, které používají místně, aniž by museli nasazovat další infrastrukturu kromě Azure AD Connect. 
   * **Předávací ověřování (PTA)** – Tato možnost je podobná synchronizaci hodnot hash hesel, ale poskytuje jednoduché ověřování hesla pomocí místních softwarových agentů pro organizace se silnými zásadami zabezpečení a dodržování předpisů.
* **Federované ověřování** – Pokud zvolíte tuto metodu ověřování, služba Azure AD odstraní proces ověřování do samostatného důvěryhodného ověřovacího systému, jako je například AD FS nebo federační systém třetí strany, aby se ověřilo přihlášení uživatele. 

Pro většinu organizací, které chtějí pouze povolit přihlašování uživatelů Microsoft 365, SaaS aplikace a další prostředky založené na službě Azure AD, doporučujeme použít výchozí možnost synchronizace hodnot hash hesel.
 
Podrobné informace o volbě metody ověřování najdete v tématu [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](./choose-ad-authn.md) .

### <a name="password-hash-synchronization"></a>Synchronizace hodnot hash hesel
Díky synchronizaci hodnot hash hesel jsou hash hesla uživatelů synchronizovaná z místní služby Active Directory do Azure AD. Když se hesla mění nebo obnoví místně, nové hodnoty hash hesel se okamžitě synchronizují do Azure AD, takže uživatelé můžou vždycky používat stejné heslo pro cloudové prostředky a místní prostředky. Hesla se nikdy neodesílají do Azure AD ani se neukládají v Azure AD v nešifrovaných textech. Můžete použít synchronizaci hodnot hash hesel společně se zpětným zápisem hesla a povolit samoobslužné resetování hesla ve službě Azure AD.

Kromě toho můžete povolit [bezproblémové jednotné přihlašování](how-to-connect-sso.md) pro uživatele na počítačích připojených k doméně, které jsou v podnikové síti. Při jednotném přihlašování stačí uživatelům zadat jenom uživatelské jméno, které jim pomůžou zajistit zabezpečený přístup k prostředkům v cloudu.

![Synchronizace hodnot hash hesel](./media/plan-connect-user-signin/passwordhash.png)

Další informace najdete v článku o [synchronizaci hodnot hash hesel](how-to-connect-password-hash-synchronization.md) .

### <a name="pass-through-authentication"></a>Předávací ověřování
Při předávacím ověřování se heslo uživatele ověřuje na místním řadiči Active Directory. Heslo nemusí být ve službě Azure AD přítomno v jakékoli formě. To umožňuje vyhodnocení místních zásad, jako jsou například omezení hodin přihlášení, které se mají vyhodnocovat při ověřování pro cloudové služby.

Předávací ověřování používá jednoduchého agenta na počítači připojeném k doméně systému Windows Server 2012 R2 v místním prostředí. Tento agent naslouchá požadavkům na ověření hesla. Nepotřebuje, aby byly na internetu otevřené žádné příchozí porty.

Kromě toho můžete také povolit jednotné přihlašování pro uživatele na počítačích připojených k doméně, které jsou v podnikové síti. Při jednotném přihlašování stačí uživatelům zadat jenom uživatelské jméno, které jim pomůžou zajistit zabezpečený přístup k prostředkům v cloudu.
![Předávací ověřování](./media/plan-connect-user-signin/pta.png)

Další informace naleznete v tématu:
- [Předávací ověřování](how-to-connect-pta.md)
- [Jednotné přihlašování](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federace používající novou nebo existující farmu s AD FS v systému Windows Server 2012 R2
Díky federovanému přihlašování se uživatelé můžou přihlašovat ke službám založeným na službě Azure AD pomocí místních hesel. I když se nacházejí v podnikové síti, nemusejí ani zadávat hesla. Pomocí možnosti federace s AD FS můžete nasadit novou nebo existující farmu s AD FS v systému Windows Server 2012 R2. Pokud se rozhodnete určit existující farmu, Azure AD Connect nakonfiguruje vztah důvěryhodnosti mezi vaší farmou a službou Azure AD tak, aby se uživatelé mohli přihlásit.

<center>

![Federace s AD FS ve Windows Serveru 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Nasazení federace s AD FS ve Windows Serveru 2012 R2

Pokud nasazujete novou farmu, budete potřebovat:

* Server se systémem Windows Server 2012 R2 pro federační server.
* Server se systémem Windows Server 2012 R2 pro server proxy webových aplikací.
* Soubor. pfx s jedním certifikátem TLS/SSL pro název vaší zamýšlené federační služby. Například: fs.contoso.com.

Pokud nasazujete novou farmu nebo používáte existující farmu, budete potřebovat:

* Přihlašovací údaje místního správce na federačních serverech.
* Přihlašovací údaje místního správce na všech serverech pracovní skupiny (nepřipojené k doméně), na které máte v úmyslu nasadit roli proxy webových aplikací.
* Počítač, na kterém jste spustili Průvodce, se bude moci připojit k ostatním počítačům, které chcete nainstalovat AD FS nebo proxy webových aplikací na serveru pomocí Vzdálená správa systému Windows.

Další informace najdete v tématu [Konfigurace jednotného přihlašování pomocí AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federace s PingFederate
Díky federovanému přihlašování se uživatelé můžou přihlašovat ke službám založeným na službě Azure AD pomocí místních hesel. I když se nacházejí v podnikové síti, nemusejí ani zadávat hesla.

Další informace o konfiguraci PingFederate pro použití s Azure Active Directory najdete v tématu [integrace PingFederate s Azure Active Directory a Office 365](https://www.pingidentity.com/AzureADConnect) .

Informace o nastavení Azure AD Connect pomocí PingFederate najdete v tématu [Azure AD Connect vlastní instalace](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) .

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Přihlaste se pomocí starší verze AD FS nebo řešení třetí strany.
Pokud jste už cloudové přihlášení nakonfigurovali pomocí starší verze AD FS (například AD FS 2,0) nebo poskytovatele federace třetích stran, můžete se rozhodnout přeskočit konfiguraci přihlašování uživatelů prostřednictvím Azure AD Connect. To vám umožní získat nejnovější synchronizaci a další funkce Azure AD Connect a přitom pořád používat vaše stávající řešení pro přihlášení.

Další informace najdete v [seznamu Kompatibilita federace třetích stran pro Azure AD](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Přihlášení uživatele a hlavní název uživatele
### <a name="understanding-user-principal-name"></a>Porozumění hlavnímu názvu uživatele
Ve službě Active Directory je výchozí příponou hlavního názvu uživatele (UPN) název DNS domény, ve které byl uživatelský účet vytvořen. Ve většině případů se jedná o název domény, který je zaregistrován jako doména organizace na internetu. Pomocí domén a vztahů důvěryhodnosti služby Active Directory ale můžete přidat další přípony UPN.

Hlavní název uživatele (UPN) má formát username@domain . Například pro doménu služby Active Directory s názvem "contoso.com" může mít uživatel s názvem Jan hlavní název uživatele (UPN) " john@contoso.com ". Hlavní název uživatele (UPN) je založený na dokumentu RFC 822. I když hlavní název uživatele (UPN) a e-mail mají stejný formát, hodnota hlavního názvu uživatele (UPN) může nebo nemusí být stejná jako e-mailová adresa uživatele.

### <a name="user-principal-name-in-azure-ad"></a>Hlavní název uživatele v Azure AD
Průvodce Azure AD Connect používá atribut userPrincipalName nebo umožňuje zadat atribut (ve vlastní instalaci), který bude použit z místního úložiště jako hlavní název uživatele v Azure AD. Jedná se o hodnotu, která se používá pro přihlášení ke službě Azure AD. Pokud hodnota atributu userPrincipalName neodpovídá ověřené doméně ve službě Azure AD, pak Azure AD ho nahradí výchozí hodnotou. onmicrosoft.com.

Každý adresář v Azure Active Directory obsahuje vestavěný název domény ve formátu contoso.onmicrosoft.com, který vám umožní začít používat Azure nebo jiné služby Microsoftu. Prostředí pro přihlašování můžete zlepšit a zjednodušit pomocí vlastních domén. Informace o vlastních názvech domén ve službě Azure AD a o tom, jak ověřit doménu, najdete v tématu [Přidání vlastního názvu domény do Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Konfigurace přihlášení k Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Konfigurace přihlášení k Azure AD pomocí Azure AD Connect
Přihlašovací prostředí Azure AD závisí na tom, jestli se může služba Azure AD shodovat s příponou hlavního názvu uživatele, která se synchronizuje k jedné z vlastních domén, které se ověřují v adresáři Azure AD. Azure AD Connect poskytuje nápovědě při konfiguraci nastavení přihlášení služby Azure AD, aby přihlášení uživatelů v cloudu bylo podobné místnímu prostředí.

Azure AD Connect zobrazí seznam přípon hlavního názvu uživatele (UPN), které jsou definovány pro domény, a pokusí se je porovnat s vlastní doménou v Azure AD. Potom vám pomůže s příslušnou akcí, kterou je třeba provést.
Přihlašovací stránka Azure AD obsahuje seznam přípon UPN definovaných pro místní službu Active Directory a zobrazuje odpovídající stav pro jednotlivé přípony. Stavové hodnoty můžou být jedna z následujících:

| State | Popis | Vyžaduje se akce |
|:--- |:--- |:--- |
| Ověřují |Azure AD Connect zjistila v Azure AD porovnávací ověřenou doménu. Všichni uživatelé této domény se můžou přihlásit pomocí svých místních přihlašovacích údajů. |Není nutné provádět žádnou akci. |
| Neověřeno |V Azure AD se Azure AD Connect najít vyhovující vlastní doména, ale není ověřená. Přípona UPN uživatelů této domény se po synchronizaci změní na výchozí příponu. onmicrosoft.com, pokud se doména neověří. | [Ověřte vlastní doménu ve službě Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Nepřidáno |Azure AD Connect nenalezl vlastní doménu, která odpovídá příponě hlavního názvu uživatele (UPN). Přípona UPN uživatelů této domény se změní na výchozí příponu. onmicrosoft.com, pokud se doména nepřidá a ověří v Azure. | [Přidejte a ověřte vlastní doménu, která odpovídá příponě hlavního názvu uživatele (UPN).](../fundamentals/add-custom-domain.md) |

Přihlašovací stránka Azure AD obsahuje seznam přípon hlavního názvu uživatele (UPN), které jsou definované pro místní službu Active Directory, a odpovídající vlastní doménu ve službě Azure AD s aktuálním stavem ověření. Ve vlastní instalaci teď můžete vybrat atribut hlavního názvu uživatele na **přihlašovací stránce služby Azure AD** .

![Přihlašovací stránka Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Kliknutím na tlačítko Aktualizovat můžete znovu načíst nejnovější stav vlastních domén z Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Výběr atributu pro hlavní název uživatele ve službě Azure AD
Atribut userPrincipalName je atribut, který uživatelé používají při přihlášení ke službě Azure AD a Microsoft 365. Před synchronizací uživatelů byste měli ověřit domény (známé také jako přípony UPN), které se používají ve službě Azure AD.

Důrazně doporučujeme, abyste zachovali výchozí atribut userPrincipalName. Pokud je tento atribut nonroutable a nelze ho ověřit, je možné vybrat jiný atribut (například e-mail) jako atribut, který obsahuje ID přihlášení. Toto je známé jako alternativní ID. Hodnota atributu alternativní ID musí odpovídat standardu RFC 822. Jako řešení pro přihlášení můžete použít alternativní ID pro jednotné přihlašování pomocí hesla i federaci jednotného přihlašování.

> [!NOTE]
> Použití alternativního ID není kompatibilní se všemi Microsoft 365 úlohami. Další informace najdete v článku [Konfigurace alternativního přihlašovacího ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Různé vlastní stavy domény a jejich vliv na přihlašovací prostředí Azure
Je velmi důležité pochopit vztah mezi vlastními stavy domén v adresáři služby Azure AD a příponami UPN, které jsou definovány místně. Podívejme se na různá možná prostředí pro přihlášení k Azure, když nastavujete synchronizaci pomocí Azure AD Connect.

Následující informace vám předpokládají, že máme obavy s příponou UPN contoso.com, která se používá v místním adresáři jako součást hlavního názvu uživatele (UPN) user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Expresní nastavení/synchronizace hodnot hash hesel

| Stav | Vliv na uživatelské prostředí Azure pro přihlašování |
|:---:|:--- |
| Nepřidáno |V tomto případě se v adresáři Azure AD nepřidala žádná vlastní doména pro contoso.com. Uživatelé, kteří mají místní uživatelské jméno (UPN) s příponou, nebudou @contoso.com moct používat svůj místní hlavní název uživatele (UPN), aby se mohli přihlásit k Azure. Místo toho budou muset použít nový hlavní název uživatele, který jim poskytla služba Azure AD, a to přidáním přípony pro výchozí adresář služby Azure AD. Pokud například provádíte synchronizaci uživatelů s adresářem Azure AD azurecontoso.onmicrosoft.com, bude místnímu uživateli user@contoso.com přiřazen hlavní název uživatele (UPN) user@azurecontoso.onmicrosoft.com . |
| Neověřeno |V tomto případě máme vlastní doménu contoso.com, která se přidá do adresáře Azure AD. Ale zatím se neověřuje. Pokud budete pokračovat s synchronizací uživatelů bez ověřování domény, pak se uživatelům přiřadí nový hlavní název uživatele (UPN) Azure AD, stejně jako ve scénáři "nepřidáno". |
| Ověřují |V tomto případě máme vlastní doménu contoso.com, která je už přidaná a ověřená ve službě Azure AD pro příponu hlavního názvu uživatele (UPN). Uživatelé budou moci použít svůj místní hlavní název uživatele, například user@contoso.com pro přihlášení k Azure po jejich synchronizaci do Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS federace
Nemůžete vytvořit federaci s výchozí doménou. onmicrosoft.com ve službě Azure AD nebo neověřenou vlastní doménou v Azure AD. Pokud při spuštění Průvodce Azure AD Connect vyberete neověřenou doménu, se kterou chcete vytvořit federaci, Azure AD Connect vás vyzve k vytvoření potřebných záznamů, kde je vaše služba DNS hostovaná pro doménu. Další informace najdete v tématu [ověření domény Azure AD vybrané pro federaci](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Pokud jste vybrali možnost přihlašování uživatele v rámci **AD FS**, musíte mít vlastní doménu, abyste mohli pokračovat v vytváření federace ve službě Azure AD. Pro naši diskuzi to znamená, že by měl být do adresáře služby Azure AD přidán vlastní contoso.com domény.

| Stav | Vliv na uživatelské prostředí Azure pro přihlašování |
|:---:|:--- |
| Nepřidáno |V takovém případě Azure AD Connect nenalezl odpovídající vlastní doménu pro příponu UPN contoso.com v adresáři služby Azure AD. Pokud potřebujete, aby se uživatelé přihlásili pomocí AD FS s místním hlavním názvem uživatele (například), musíte přidat vlastní doménu contoso.com user@contoso.com . |
| Neověřeno |V takovém případě vás Azure AD Connect vyzve s příslušnými podrobnostmi o tom, jak můžete doménu ověřit v pozdější fázi. |
| Ověřují |V takovém případě můžete pokračovat v konfiguraci bez jakékoli další akce. |

## <a name="changing-the-user-sign-in-method"></a>Změna metody přihlašování uživatelů
Metodu přihlašování uživatelů můžete změnit z federace, synchronizace hodnot hash hesel nebo předávacího ověřování pomocí úloh, které jsou k dispozici v Azure AD Connect po počáteční konfiguraci Azure AD Connect pomocí průvodce. Spusťte znovu Průvodce Azure AD Connect a zobrazí se seznam úkolů, které můžete provádět. V seznamu úkolů vyberte **změnit přihlášení uživatele** .

![Změna přihlášení uživatele](./media/plan-connect-user-signin/changeusersignin.png)

Na další stránce budete požádáni o zadání přihlašovacích údajů pro Azure AD.

![Snímek obrazovky, který ukazuje, kde byste měli zadat přihlašovací údaje pro Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Na **přihlašovací stránce uživatele** vyberte přihlášení požadovaného uživatele.

![Připojení k Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Pokud vytváříte pouze dočasný přepínač pro synchronizaci hodnot hash hesel, zaškrtněte políčko **nepřevádět uživatelské účty** . Pokud se nerozhodnete, tato možnost převede každého uživatele na federované a může trvat několik hodin.
>
>

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [integraci místních identit s Azure Active Directory](whatis-hybrid-identity.md).
- Přečtěte si další informace o [Azure AD Connect konceptech návrhu](plan-connect-design-concepts.md).