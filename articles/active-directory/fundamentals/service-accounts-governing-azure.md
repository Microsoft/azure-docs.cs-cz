---
title: Řízení Azure Active Directory účtů služeb
description: Zásady a postupy pro správu životního cyklu účtů služeb v Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee6ac21d67f32fbc61db19b348fc29cdf3ee9fd7
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2021
ms.locfileid: "103418177"
---
# <a name="governing-azure-ad-service-accounts"></a>Řízení účtů služby Azure AD

V Azure Active Directory (Azure AD) Existují tři typy účtů služeb: [spravované identity](service-accounts-managed-identities.md), [instanční objekty](service-accounts-principal.md)a uživatelské účty používané jako účty služeb. Při vytváření těchto účtů služeb pro automatizované použití jsou jim udělena oprávnění k přístupu k prostředkům v Azure a Azure AD. Prostředky můžou zahrnovat Microsoft 365 služby, aplikace SaaS (software jako služba), vlastní aplikace, databáze, systémy HR a tak dále. Řízení účtů služby Azure AD znamená, že budete spravovat jejich vytváření, oprávnění a životní cyklus, abyste zajistili zabezpečení a kontinuitu.

> [!IMPORTANT] 
> Nedoporučujeme používat uživatelské účty jako účty služeb, protože jsou v podstatě méně zabezpečené. Patří sem účty místních služeb, které se synchronizují do služby Azure AD, protože nejsou převedené na instanční objekty. Místo toho doporučujeme použití spravovaných identit nebo instančních objektů. Všimněte si, že v tuto chvíli není možné použití zásad podmíněného přístupu u instančních objektů, ale funkce přichází.


## <a name="plan-your-service-account"></a>Plánování účtu služby

Před vytvořením účtu služby nebo registrací aplikace, zdokumentujte informace o klíčích účtu služby. S dokumentovanými informacemi je snazší efektivně monitorovat a řídit účet. Doporučujeme shromažďovat následující data a sledovat je v centralizované databázi správy konfigurace (CMDB).

| Data| Description| Podrobnosti |
| - | - | - |
| Vlastník| Uživatel nebo skupina, které jsou pro správu a monitorování účtu služby účtovány.| Zřiďte vlastníka s potřebnými oprávněními ke sledování účtu a implementaci způsobu, jak zmírnit problémy. Zmírnění problémů může provést vlastník nebo prostřednictvím žádosti. |
| Účel| Jak bude účet použit.| Namapujte účet služby na konkrétní službu, aplikaci nebo skript. Vyhněte se vytváření účtů služeb s vícenásobným použitím. |
| Oprávnění (obory)| Očekávaná sada oprávnění.| Zdokumentujte prostředky, ke kterým bude mít přístup, a oprávnění k těmto prostředkům. |
| Odkaz CMDB| Odkaz na prostředky, které se mají použít, a skripty, ve kterých se používá účet služby| Ujistěte se, že máte v dokumentu vlastníci pro prostředky a skripty, abyste mohli komunikovat s potřebnými důsledky změn. |
| Posouzení rizik| Rizika a dopad na firmu v případě ohrožení zabezpečení účtu.| Tyto informace slouží k zúžení rozsahu oprávnění a určení toho, kdo má mít přístup k informacím o účtu. |
| Období pro kontrolu| Plán, ve kterém má být účet služby přezkoumán vlastníkem.| Toto použijte k naplánování kontroly komunikace a recenzí. Dokument, co se má stát v případě, že kontrola není provedena určitou dobu po plánovaném období kontroly. |
| Doba platnosti| Předpokládaná maximální doba života účtu.| Tuto akci použijte k naplánování komunikace s vlastníkem a k jejich konečnému zakázání pak odstraňte účty. Pokud je to možné, nastavte datum vypršení platnosti přihlašovacích údajů, kde se přihlašovací údaje nedají automaticky navrátit. |
| Name| Standardizovaný název účtu| Vytvořte schéma pojmenování pro všechny účty služeb, abyste mohli snadno vyhledávat, třídit a filtrovat účty služeb. |


## <a name="use-the-principle-of-least-privileges"></a>Použijte princip nejnižších oprávnění.
Udělte účtu služby jenom oprávnění potřebná k provádění svých úkolů a žádné další. Pokud účet služby potřebuje oprávnění vysoké úrovně, například globální úroveň správce oprávnění, vyhodnoťte důvod a pokuste se snížit potřebná oprávnění.

Pro oprávnění účtu služby doporučujeme použít následující postupy.

**Oprávnění**

* Nepřiřazujte k účtům služeb předdefinované role. Místo toho použijte [pro Microsoft Graph model udělení oprávnění OAuth2](/graph/api/resources/oauth2permissiongrant),

* Pokud musí být instančnímu objektu přiřazena privilegovaná role, zvažte možnost přiřazení [vlastní role](https://docs.microsoft.com/azure/active-directory/roles/custom-create) se specifickými a požadovanými privilegovanými daty v čase.

* Nezahrnovat účty služeb jako členy žádné skupiny se zvýšenými oprávněními. 

* [Pomocí PowerShellu můžete vytvořit výčet členů privilegovaných rolí](/powershell/module/azuread/get-azureaddirectoryrolemember), jako je například   
`Get-AzureADDirectoryRoleMember`a vyfiltrujte objekt instančního objektu ObjectType.

   nebo použijte  
`Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

* [Použijte rozsahy OAuth 2,0](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) k omezení funkčnosti, ke kterým má účet služby přístup, k prostředku.
* Instanční objekty a spravované identity můžou používat rozsahy OAuth 2,0 v delegovaném kontextu, který zosobňuje přihlášeného uživatele, nebo jako účet služby v kontextu aplikace. V kontextu aplikace není k dispozici přihlášení.

* Zkontrolujte, jestli účty služeb oborů požadují prostředky, abyste měli jistotu, že jsou vhodné. Pokud třeba účet vyžaduje soubor. Readme. All, vyhodnoťte, jestli ve skutečnosti potřebuje jenom File. Read. All. Další informace o oprávněních najdete v tématu [Microsoft Graph odkaz na oprávnění](https://docs.microsoft.com/graph/permissions-reference).

* Ujistěte se, že vývojářům aplikace nebo rozhraní API důvěřujete s přístupem požadovaným pro vaše prostředky.

**Doba trvání**

* Omezení přihlašovacích údajů účtu služby (tajný klíč klienta, certifikát) na předpokládanou dobu používání.

* Naplánování pravidelné kontroly používání a účelu účtů služeb Před vypršením platnosti účtu ověřte, zda jsou kontroly provedeny.

Jakmile budete mít jasné porozumění účelu, rozsahu a potřebných oprávnění, vytvořte účet služby. 

[Vytváření a používání spravovaných identit](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

[Vytváření a používání instančních objektů](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)

Pokud je to možné, použijte spravovanou identitu. Pokud nemůžete použít spravovanou identitu, použijte instanční objekt. Pokud nemůžete použít instanční objekt, pak a jenom pak použijte uživatelský účet služby Azure AD.

 

## <a name="build-a-lifecycle-process"></a>Sestavení procesu životního cyklu

Správa životního cyklu účtu služby začíná plánováním a končí trvalém odstraněním. 

V tomto článku se dřív pokryla část plánování a vytváření. Musíte také monitorovat, kontrolovat oprávnění, určit nepřetržité používání účtu a nakonec účet zrušit.

### <a name="monitor-service-accounts"></a>Monitorování účtů služeb

Proaktivně monitorujte účty služeb a ujistěte se, že vzory využití účtu služby odpovídají zamýšleným vzorům a že účet služby se pořád aktivně používá.

**Shromážděte a sledujte přihlášení k účtu služby pomocí jedné z následujících metod:**

* Použití protokolů Sign-In služby Azure AD na portálu Azure AD.

* Export protokolů Sign-In služby Azure AD do [Azure Storage](https://docs.microsoft.com/azure/storage/), [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/)nebo [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/logs/data-platform-logs).


![Snímek obrazovky znázorňující obrazovku pro přihlášení k objektu služby](./media/securing-service-accounts/service-accounts-govern-azure-1.png)

**Informace, které byste měli hledat v protokolech Sign-In, zahrnují:**

* Jsou k dispozici účty služeb, které se již přihlásí k tenantovi?

* Mění se vzor přihlašování účtů služeb?

Doporučujeme vám exportovat protokoly přihlášení do služby Azure AD a importovat je do stávajících nástrojů SIEM (Security Information and Event Management), jako je například Azure Sentinel. Pomocí SIEM můžete sestavovat výstrahy a řídicí panely.

### <a name="review-service-account-permissions"></a>Kontrola oprávnění účtu služby

Pravidelně kontrolujte udělená oprávnění a obory, k nimž mají přístup účty služby, a zjistěte, jestli je možné je omezit.

* Použijte [PowerShell](/powershell/module/azuread/get-azureadserviceprincipaloauth2permissiongrant) k [sestavení automatizace pro kontrolu a dokumentaci](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) oborů, ke kterým je udělen souhlas účtu služby.

* Pomocí PowerShellu [Zkontrolujte přihlašovací údaje existujících instančních objektů](https://github.com/AzureAD/AzureADAssessment) a zkontrolujte jejich platnost.

* Nenastavujte přihlašovací údaje instančního objektu na nikdy nevyprší platnost.

* Pokud je to možné, používejte certifikáty nebo přihlašovací údaje uložené v Azure webtrezoru.

Bezplatný ukázkový PowerShell od Microsoftu shromažďuje OAuth2 a přihlašovací údaje instančního objektu, zaznamenává je do souboru hodnot oddělených čárkami (CSV) a Power BI ukázkový řídicí panel pro interpretaci a používání dat. Další informace najdete v tématu [AzureAD/AzureADAssessment: nástroje pro vyhodnocování stavu a konfigurace tenanta Azure AD (GitHub.com).](https://github.com/AzureAD/AzureADAssessment)

### <a name="recertify-service-account-use"></a>Znovu certifikovat použití účtu služby

Zřízení procesu revize za účelem zajištění pravidelného přezkoumání účtů služeb svými vlastníky a týmy zabezpečení nebo IT v pravidelných intervalech. 

**Proces by měl zahrnovat:**

* Jak určit cyklus revizí jednotlivých účtů služby (musí být dokumentován ve vaší organizaci CMDB).

* Komunikace s vlastníkem a zabezpečením nebo IT týmy před zahájením recenze.

* Načasování a obsah upozornění na komunikaci, není-li kontrola vynechána.

* Pokyny k tomu, co dělat v případě, že se vlastníci nepodaří zkontrolovat nebo odpovědět. Například můžete chtít účet zakázat (ale ne odstranit), dokud se kontrola nedokončí.

* Pokyny týkající se určování nadřazených a podřízených závislostí a oznamování dalších vlastníků prostředků u všech efektů.

**Tato kontrola by měla zahrnovat vlastníka a jeho partner IT, který potvrzuje, že:**

* Účet je stále nutný.

* Oprávnění udělená účtu jsou dostačující a nezbytná nebo se vyžaduje změna.

* Přístup k účtu a jeho přihlašovací údaje se řídí.

* Přihlašovací údaje, které účet používá, jsou vhodné v souvislosti s rizikem, kdy se účet vyhodnotil (typ přihlašovacích údajů i doba platnosti přihlašovacích údajů).

* Hodnocení rizik účtu se od posledního recertifikace nezměnilo.

* Aktualizace očekávané životnosti účtu a další datum potvrzení.

### <a name="deprovision-service-accounts"></a>Zrušit zřízení účtů služby

* * Zrušit zřízení účtů služby za následujících okolností: * * * *

* Skript nebo aplikace, pro které byl účet služby vytvořen, je vyřazený.

* Funkce v rámci skriptu nebo aplikace, ke které se používá účet služby (například přístup ke konkrétnímu prostředku), je vyřazená.


* Účet služby je nahrazen jiným účtem služby.

* Přihlašovací údaje vypršely, nebo je účet jinak nefunkční a neexistují žádné stížnosti.

**Procesy zrušení zřízení by měly zahrnovat následující úkoly.**

1. Po zrušení zřízení přidružené aplikace nebo skriptu [monitorujte přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#sign-ins-report) a přístup k prostředkům pomocí účtu služby.

   * Pokud je účet stále aktivní, před provedením následujících kroků určete, jak se používá.
 
2. Pokud se jedná o identitu spravované služby, zakažte účet služby před přihlášením, ale neodstraňujte ho z adresáře.

3. Odvolání přiřazení rolí a udělení souhlasu OAuth2 pro účet služby.

4. Po definovaném období a dostatečné varování pro vlastníky odstraňte účet služby z adresáře.

## <a name="next-steps"></a>Další kroky
Další informace o zabezpečení účtů služeb Azure najdete v těchto tématech:

[Seznámení s účty služeb Azure](service-accounts-introduction-azure.md)

[Zabezpečení spravovaných identit](service-accounts-managed-identities.md)

[Zabezpečení principů služby](service-accounts-principal.md)




 

 
