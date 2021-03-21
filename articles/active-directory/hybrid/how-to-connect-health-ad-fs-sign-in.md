---
title: AD FS přihlášení ve službě Azure AD s připojením Health | Microsoft Docs
description: Tento dokument popisuje, jak integrovat AD FS přihlášení pomocí sestavy Azure AD Connect Health přihlášení.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574786"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS přihlášení ve službě Azure AD s připojením Health – Preview

Přihlašovací AD FS se teď dají integrovat do sestavy Azure Active Directory přihlášení pomocí funkce Connect Health. Sestava [přihlášení k Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.) obsahuje informace o tom, kdy se uživatelé, aplikace a spravované prostředky přihlásí ke službě Azure AD a přistupuje k prostředkům. 

Agent Connect Health pro AD FS koreluje více ID událostí z AD FS, závislých na verzi serveru, aby poskytoval informace o požadavku a podrobnostech o chybě, pokud požadavek selhává. Tyto informace se přihlásily ke schématu sestav přihlášení k Azure AD a zobrazují se v uživatelském rozhraní sestav Sign-In služby Azure AD. Vedle sestavy je k dispozici nový datový proud Log Analytics s daty AD FS a novou šablonou sešitu Azure Monitor. Šablonu lze použít a upravit pro podrobnou analýzu scénářů, jako jsou například uzamčení účtů AD FS, špatné pokusy o zadání hesla a špičky neočekávaných pokusů o přihlášení.

## <a name="prerequisites"></a>Předpoklady
* Azure AD Connect Health pro AD FS nainstalované a upgradované na nejnovější verzi.
* Role globálního správce nebo čtenáře sestav pro zobrazení přihlášení k Azure AD

## <a name="what-data-is-displayed-in-the-report"></a>Jaká data se zobrazí v sestavě?
Data jsou k dispozici zrcadlová data, která jsou k dispozici pro přihlášení Azure AD. Na základě typu přihlášení bude k dispozici pět karet s informacemi, a to buď Azure AD, nebo AD FS. Connect Health koreluje události z AD FS, závislé na verzi serveru a odpovídá AD FS schématu. 



#### <a name="user-sign-ins"></a>Přihlášení uživatelů 
Každá karta v okně přihlášení zobrazuje výchozí hodnoty níže:
* Datum přihlášení
* ID požadavku
* Uživatelské jméno nebo ID uživatele
* Stav přihlášení
* IP adresa zařízení, které se používá pro přihlášení
* Identifikátor Sign-In

#### <a name="authentication-method-information"></a>Informace o metodě ověřování
Na kartě ověřování mohou být zobrazeny následující hodnoty. Metoda ověřování je pořízena z protokolů auditu AD FS.

|Metoda ověřování|Description|
|-----|-----|
|Formuláře|Ověřování uživatelského jména a hesla|
|Windows|Ověřování systému Windows – integrované|
|Certifikát|Ověřování s využitím certifikátů SmartCard/VirtualSmart|
|WindowsHelloForBusiness|Toto pole je pro ověřování pomocí Windows Hello pro firmy. (Microsoft Passport ověřování)|
|Zařízení | Zobrazuje se, pokud je vybráno ověřování zařízení jako primární ověřování z intranetu nebo extranetu a provádí se ověřování zařízení.  V tomto scénáři neexistuje žádné samostatné ověřování uživatelů.| 
|Federovaní|AD FS neproběhlo ověřování, ale odeslal ho poskytovateli identity třetí strany.|
|Jednotné přihlašování |Pokud byl použit token jednotného přihlašování, toto pole se zobrazí. Pokud má jednotné přihlašování MFA, zobrazí se jako vícefaktorového.|
|Vícefaktorové|Pokud má token jednotného přihlašování MFA, který se použil pro ověřování, toto pole se zobrazí jako vícefaktorového.|
|Azure MFA|Služba Azure MFA je vybrána jako další zprostředkovatel ověřování v AD FS a byla použita pro ověřování.|
|ADFSExternalAuthenticationProvider|Toto pole je v případě, že zprostředkovatel ověřování třetí strany byl zaregistrován a používán pro ověřování.|


#### <a name="ad-fs-additional-details"></a>AD FS další podrobnosti
Pro AD FS přihlášení jsou k dispozici následující podrobnosti:
* Název serveru
* Řetězec IP
* Protokol

### <a name="enabling-log-analytics-and-azure-monitor"></a>Povolení Log Analytics a Azure Monitor
Log Analytics lze povolit pro AD FS přihlášení a lze je použít s ostatními Log Analytics integrovanými komponentami, jako je například Sentinel.

> [!NOTE] 
> AD FS přihlášení může výrazně zvýšit náklady Log Analytics, a to v závislosti na množství přihlášení ke AD FS ve vaší organizaci. Pokud chcete povolit nebo zakázat Log Analytics, zaškrtněte políčko pro datový proud.

Pokud chcete povolit Log Analytics pro funkci, přejděte do okna Log Analytics a vyberte datový proud "ADFSSignIns". Tento výběr umožní, aby se AD FS přihlášení do Log Analytics Flow.

Pokud chcete získat přístup k aktualizované šabloně sešitu Azure Monitor, přejděte na "šablony Azure Monitor" a vyberte sešit "přihlášení".
Další informace o sešitech najdete v [Azure monitor sešitech](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Nejčastější dotazy
***Jaké typy přihlášení se mi můžou zobrazit?***
Sestava přihlášení podporuje přihlášení prostřednictvím protokolů O-ověření, WS-dodávání, SAML a WS-Trust. 

***Jak se v sestavě přihlášení zobrazují různé typy přihlášení?***
Pokud se provede bezproblémové přihlašování SSO, bude pro přihlášení jeden řádek s jedním ID korelace.
Pokud se provádí ověřování jedním faktorem, budou se za dva řádky naplnit stejné ID korelace, ale dvě různé metody ověřování (například formuláře, jednotné přihlašování).
V případě Multi-Factor Authentication budou existovat tři řádky se sdíleným ID korelace a tři odpovídající metody ověřování (například formuláře, AzureMFA, vícefaktorového). V tomto konkrétním příkladu se u jednotného faktu v tomto případě ukáže, že jednotné přihlašování má MFA.

***Jaké jsou chyby, které vidím v sestavě?***
Úplný seznam AD FS souvisejících s chybami, které jsou vyplněny v sestavě Sign-In a popisech, najdete v [tématu AD FS odkaz na kód chyby](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference) .

***V části "uživatele" přihlášení se zobrazuje "00000000-0000-0000-0000-000000000000". Co to znamená?***
Pokud se nezdařilo přihlášení a pokusy hlavního názvu uživatele (UPN) se neshodují s existujícím hlavním názvem uživatele (UPN), budou pole "uživatel", "uživatelské jméno" a "ID uživatele" "00000000-0000-0000-0000-000000000000" a "identifikátor přihlášení" bude vyplněn hodnotou zadanou uživatelem. V těchto případech se uživatel, který se pokouší přihlásit, neexistuje.

***Jak mohu sladit místní události se sestavou přihlášení k Azure AD?***
Agent Azure AD Connect Health pro AD FS koreluje ID událostí z AD FS závislé na verzi serveru. Události budou k dispozici v protokolu zabezpečení AD FSch serverů. 

***Proč se mi v ID nebo názvu aplikace zobrazuje nebo NotApplicable, pro některá AD FS přihlášení?***
Sestava AD FS Sign-In zobrazí v poli ID aplikace identifikátory OAuth pro přihlášení OAuth. Ve scénářích pro přihlašování WS-Trust WS-in, ID aplikace bude mít hodnotu NotSet nebo NotApplicable a ID prostředků a identifikátory předávající strany budou k dispozici v poli ID prostředku.

***Existují nějaké další známé problémy se sestavou ve verzi Preview?***
Tato sestava obsahuje známý problém, kdy se pole "požadavek na ověření" na kartě "základní informace" vyplní jako hodnota jediného faktoru ověřování pro AD FS přihlášení bez ohledu na přihlášení. Na kartě Podrobnosti o ověřování se navíc v poli požadavek zobrazí "primární nebo sekundární", s opravou Probíhá oprava pro odlišení primárního nebo sekundárního typu ověřování.


## <a name="related-links"></a>Související odkazy
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalace agenta Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Sestava rizikových IP adres](how-to-connect-health-adfs-risky-ip.md)





