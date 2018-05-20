---
title: Azure AD Connect Health - diagnostikovat duplicitní atribut chyby synchronizace | Microsoft Docs
description: Tento dokument popisuje proces Diagnostikujte chyby synchronizace duplicitní atribut a opravte potenciální scénářů osamocený objekt přímo z portálu Azure.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: maheshu
editor: billmath
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: zhiweiw
ms.openlocfilehash: 0a345fd3443fb33d6f5912c8a04677091e20ecc8
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="duplicate-attribute-sync-errors-diagnosis-and-remediation"></a>Duplicitní atribut synchronizace chyby diagnostiky a nápravy 

## <a name="overview"></a>Přehled
Pořízení ještě o krok napřed zvýraznění chyby synchronizace, Azure Active Directory Connect Health představuje samoobslužnou nápravu prostředí řešení duplicitní atribut synchronizace chyb a opravte osamocených objektů z Azure AD. Hlavní výhoda z diagnostiku funkce:
- Zadejte diagnostiky postup zúžit duplicitní atribut synchronizace chyba scénáře a uvést konkrétní řešení
- Použít opravu pro vyhrazené scénáře z Azure AD, chcete-li vyřešit chyby v jedním kliknutím
- Chcete-li povolit tuto funkci je zapotřebí žádné upgrade nebo konfigurace.
Přečtěte si další informace o Azure AD [duplicitní odolnost](https://aka.ms/dupattributeresdocs).

## <a name="problems"></a>Problémy
### <a name="common-scenario"></a>Běžný scénář
Když **QuarantinedAttributeValueMustBeUnique** a **AttributeValueMustBeUnique** chyby synchronizace dojít, je běžné zobrazíte konflikt hlavního názvu uživatele nebo adresy proxy serveru ve službě Azure AD. Chyby synchronizace může vyřešit tím, že aktualizuje konfliktní zdrojového objektu aplikace na straně místní. Chyba synchronizace bude vyřešen po následující synchronizace. Na obrázku níže například označuje, že jsou dva uživatelé s konflikt jejich UserPrincipalName jako *Joe.J@contoso.com*. Konfliktní objekty jsou v karanténě ve službě Azure AD. 

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

### <a name="orphaned-object-scenario"></a>Scénář osamocený objekt
Příležitostně můžete zjistit, že zdrojové ukotvení ztratí stávajícího uživatele. Došlo k odstranění zdrojový objekt v místní AD, ale změnu odstranění signál tu nikdy synchronizován do služby Azure AD. K tomu může dojít z důvodů, jako je například synchronizační modul problém nebo migraci domény. Pokud stejný objekt získali obnovit nebo znovu vytvořen, logicky stávajícího uživatele musí být uživateli synchronizovat ze zdrojové ukotvení. Pro stávajícího uživatele jako pouze objekt cloudu můžete také zjistit konfliktní uživatele synchronizovat se službou Azure AD a nelze porovnat synchronizace pro existující objekt. Neexistuje žádné přímé způsob přemapování zdrojové ukotvení. Další informace o [existující KB](https://support.microsoft.com/help/2647098). Existující objekt ve službě Azure AD například uchovává licence Jan. Nově synchronizovaných objekt s jinou zdrojové ukotvení došlo k chybě ve stavu duplicitní atribut ve službě Azure AD. Změny systému Jan v místní AD nepůjde má být použita pro původního uživatele Michalův (existující objekt) ve službě Azure AD.  

![Diagnostika scénář osamocený objekt Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

## <a name="diagnostic-and-troubleshooting-steps-in-connect-health"></a>Diagnostika a řešení potíží s kroky v Connect Health 
Diagnostika podporuje funkce uživatelské objekty s následující duplicitní atributy:

| Název atributu | Typy chyb synchronizace|
| ------------------ | -----------------|
| UserPrincipalName | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| ProxyAddresses | QuarantinedAttributeValueMustBeUnique nebo AttributeValueMustBeUnique | 
| sipProxyAddress | AttributeValueMustBeUnique | 
| onPremiseSecurityIdentifier |  AttributeValueMustBeUnique |

>[!IMPORTANT]
> Vyžaduje **globálního správce** oprávnění nebo **Přispěvatel** z RBAC nastavení, abyste mohli používat tuto funkci. 
>

Následující kroky z portálu Azure budete moct zúžit podrobnosti o chybě synchronizace a poskytnout další specifické řešení:

![Synchronizace diagnostikovat chyby diagnostikovat kroky](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixSteps.png)

Z portálu Azure bude možné projít pár kroků k identifikaci konkrétní opravitelné scénáře:  
1.  Ve sloupci Stav Diagnostikujte se zobrazí stav, pokud je potenciální řešení potíží toky zúžit případ chyby a potenciálně opravit přímo ze služby Azure Active Directory.
| Status | Co to znamená? |
| ------------------ | -----------------|
| Nezahájeno | Tento proces diagnostiku nebyly navštívil. Závisí na diagnostiky výsledků potenciálně neexistuje způsob, jak přímo opravte chybu synchronizace z portálu. |
| Vyžaduje se ruční oprava. | Chyba nevejde do kritéria k dispozici oprava z portálu. Případě může být (1) konfliktní objekt, který typy nejsou, že uživatelé (2) je již se pomocí kroků diagnostiky a ne opravte řešení, které jsou k dispozici z portálu. V takovém případě opravte ze strany místní zůstanou jedno z řešení. [Další informace o místní oprava](https://support.microsoft.com/help/2647098) | 
| Čeká se na synchronizaci. | Oprava byla použita. Čekání na další cyklus synchronizace vymazat chybu. |
>[!IMPORTANT]
> Sloupec stavu diagnostiky se obnoví po každém cyklu synchronizace. 
>

2.  Kliknutím na **Diagnostikujte** tlačítka v okně podrobností chyby, budete muset odpovědět na několik otázek a identifikovat podrobnosti o chybě synchronizace. Zodpovězení otázek pomůže identifikovat v případě scénáře osamocený objekt. 

3.  Pokud dojde **Zavřít** tlačítko na konci Diagnostika, znamená to není k dispozici z portálu na základě daného odpovědí žádné rychlé opravy. Naleznete řešení uvedené v předchozím kroku. Oprava z místní budou mít pořád řešení. Po kliknutí na tlačítko Zavřít, zjistíte, se změní stav aktuální Chyba synchronizace se **Ruční oprava požadované**. Stav zachovají během aktuální synchronizační cyklus.

4.  Jakmile se identifikuje osamocený objekt případ, bude možné chyby synchronizace přímo z portálu odstranit duplicitní atributy. Klikněte na **použít opravte** tlačítko Spustit proces. Stav aktuální Chyba synchronizace se aktualizuje a být **čekající na vyřízení synchronizace**.

5.  Následující synchronizace cyklu chyba byste měli odebrat ze seznamu.

## <a name="how-to-answer-the-diagnosis-questions"></a>Postup odpovězte na otázky diagnostiku 
### <a name="does-the-user-exist-in-your-on-premises-active-directory"></a>Uživatel existuje v místních služby Active Directory?

Otázka se pokouší identifikovat zdrojový objekt existujícího uživatele z místní služby Active Directory.  
1.  Zkontrolujte, jestli služby Active Directory má objekt s zadaný UserPrincipalName. Pokud ne, odpovězte ne.
2.  Pokud ano, zkontrolujte, jestli je objekt stále v oboru synchronizace.  
  - Prohledejte prostor konektoru Azure AD pomocí DN.
  - Pokud je nalezen objekt s **čekající přidání** stavu, odpovězte ne. Azure AD Connect není možné se připojit objekt na správný objekt AD.
  - Pokud není nalezen objekt, odpovíte kladně.

> S ohledem na následujícím diagramu, například na otázku se pokouší zjistit, zda *Jan Jackson* nacházet v místní službě Active Directory.
Pro **běžný scénář**, oba uživatele *Jan Janíček* a *Jan Jackson* bude k dispozici ve vaší místní službou Active Directory. V karanténě objekty jsou dvě různé uživatele.

![Diagnostika běžný scénář Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixCommonCase.png)

> Pro **osamocený objekt scénář**, pouze jednoho uživatele – *Jan Janíček* bude k dispozici z místní služby Active Directory:

![Diagnostika scénář osamocený objekt Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)

### <a name="do-both-these-accounts-belong-to-the-same-user"></a>Patří oba tyto účty jednomu uživateli?
Otázka kontroluje příchozí konfliktní uživatele a je existující objekt uživatele ve službě Azure AD najdete v případě, že patří do stejného uživatele.  
1.  Konfliktní objektu je nově synchronizovat do Azure Active Directory. Porovnání objekt z jeho:  
  - Zobrazované jméno
  - Hlavní název uživatele (UPN)
  - ID objektu
2.  Pokud Nepodařilo se porovnat je, zkontrolujte, že má objekty s zadaný UserPrincipalNames služby Active Directory. Odpovíte ZÁPORNĚ, pokud obě nebyly nalezeny.  

> V případě níže dva objekty patří do stejného uživatele *Jan Janíček*.

![Diagnostika scénář osamocený objekt Chyba synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixOrphanedCase.png)


## <a name="what-happened-after-fix-is-applied-for-orphaned-object-scenario"></a>Co se stalo po použití opravy pro scénář osamocený objekt
Na základě odpovědí vyvolané otázek, nebudete moci zobrazit **použít opravte** tlačítko, je-li k dispozici z Azure AD opravu. V takovém případě je objekt na místní synchronizace s neočekávanou Azure AD objektu. Tyto dva objekty jsou mapovány pomocí "Zdrojové ukotvení". Použít změny provede kroky, jako:
- Aktualizujte zdrojové ukotvení na správný objekt ve službě Azure AD.
- Odstraňte konfliktní objekt ve službě Azure AD, pokud představuje.

![Po potíže diagnostikovat chyby synchronizace](./media/active-directory-aadconnect-health-sync-iidfix/IIdFixAfterFix.png)

>[!IMPORTANT]
> Změna použít oprava se vztahuje pouze na osamocený objekt případy.
>

Po výše uvedených kroků bude uživatel moct přistupovat k původní prostředku, který je odkaz na existující objekt. **Diagnostikovat stav** hodnota v zobrazení seznamu bude aktualizována na **čekající synchronizace**. Chyba synchronizace bude vyřešen po následující synchronizace. Připojení stavu už nezobrazí chyba přeložit synchronizace ze zobrazení seznamu. 


## <a name="faq"></a>Nejčastější dotazy
 -  Co se stalo, pokud se nezdařilo spuštění použít?  
Pokud se spuštění nezdaří, je možné Azure AD Connect běží Chyba exportu v době. Aktualizujte stránky portálu a opakujte po následující synchronizaci. Výchozí synchronizační cyklus je 30 minut. 

 -  Co když **existující objekt** by měla být objekt, který chcete odstranit?  
Pokud v tomto případě měla by být odstraněna existující objekt, proces nezahrnuje změnu zdrojové ukotvení. Nyní byste měli mít opravte z vaší místní AD.  

 -  Co je oprávnění pro uživatele, abyste mohli použít opravu?  
Globální správce nebo Přispěvatel z RBAC nastavení bude mít oprávnění pro přístup k diagnostiky a řešení potíží s procesem.

 -  Muset konfigurace AAD Connect nebo aktualizovat agenta Azure AD Connect Health pro tuto funkci?  
Ne, diagnostiku proces je kompletní cloudové funkce.

 -  Pokud existující objekt je logicky odstranit, bude proces Diagnostikujte obnovit objekt, který má být znovu aktivní?  
Ne, nebude opravu aktualizovat atribut objektu než zdrojové ukotvení. 
