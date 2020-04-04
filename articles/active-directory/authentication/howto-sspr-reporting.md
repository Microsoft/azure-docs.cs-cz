---
title: Samoobslužné sestavy pro resetování hesla – Azure Active Directory
description: Vytváření sestav samoobslužných událostí pro resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 348082ad96a5efa4d8f866c3675044edf7b6f8a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652158"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Možnosti vytváření sestav správy hesel v Azure AD

Po nasazení mnoho organizací chce vědět, jak nebo zda je samoobslužné resetování hesla (SSPR) skutečně používáno. Funkce vytváření sestav, kterou služba Azure Active Directory (Azure AD) poskytuje, vám pomůže zodpovědět otázky pomocí předem sestavených sestav. Pokud máte příslušnou licenci, můžete také vytvořit vlastní dotazy.

![Vytváření sestav na sspr pomocí protokolů auditu ve službě Azure AD][Reporting]

Na následující otázky mohou odpovědět sestavy, které existují na [webu Azure Portal](https://portal.azure.com/):

> [!NOTE]
> Musíte být [globálním správcem](../users-groups-roles/directory-assign-admin-roles.md)a musíte se přihlásit, aby tato data byla shromažďována jménem vaší organizace. Chcete-li se přihlásit, musíte alespoň jednou navštívit kartu **Přehledy** nebo protokoly auditu. Do té doby nejsou data shromažďována pro vaši organizaci.
>

* Kolik lidí se zaregistrovalo pro resetování hesla?
* Kdo se zaregistroval k obnovení hesla?
* Jaké údaje lidé registrují?
* Kolik lidí resetuje svá hesla za posledních sedm dní?
* Jaké jsou nejběžnější metody, které uživatelé nebo správci používají k resetování hesel?
* Jaké jsou běžné problémy, kterým uživatelé nebo správci čelí při pokusu o použití resetování hesla?
* Kteří správci často resetují vlastní hesla?
* Dochází při resetování hesla k nějaké podezřelé aktivitě?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Zobrazení sestav správy hesel na webu Azure Portal

V prostředí portálu Azure jsme vylepšili způsob, jakým můžete zobrazit aktivitu obnovení hesla a registrace hesla. Pomocí následujících kroků vyhledejte události registrace pro obnovení hesla a obnovení hesla:

1. Přejděte na [portál Azure](https://portal.azure.com).
2. V levém podokně vyberte **Všechny služby**.
3. Vyhledejte **službu Azure Active Directory** v seznamu služeb a vyberte ji.
4. V části Spravovat vyberte **Uživatelé.**
5. V okně **Uživatelé** vyberte **protokoly auditování.** Zobrazí se všechny události auditu, ke kterým došlo proti všem uživatelům ve vašem adresáři. Toto zobrazení můžete filtrovat a zobrazit všechny události související s heslem.
6. V nabídce **Filtr** v horní části podokna vyberte rozevírací seznam **Služba** a změňte ho na typ služby **Samoobslužná správa hesel.**
7. Volitelně můžete seznam dále filtrovat výběrem konkrétní **aktivity,** která vás zajímá.

### <a name="converged-registration-preview"></a>Selhovatá registrace (náhled)

Pokud se účastníte veřejné verze Preview konvergované registrace, informace o aktivitě uživatele v protokolech auditu najdete v části**Metody ověřování** **zabezpečení** > .

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Popis sloupců sestavy na webu Azure Portal

Následující seznam podrobně vysvětluje každý sloupec sestavy na webu Azure Portal:

* **Uživatel**: Uživatel, který se pokusil o operaci registrace obnovení hesla.
* **Role**: Role uživatele v adresáři.
* **Datum a čas**: Datum a čas pokusu.
* **Registrovaná data**: Ověřovací data, která uživatel poskytl při registraci resetování hesla.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Popis hodnot sestavy na webu Azure Portal

Následující tabulka popisuje různé hodnoty, které můžete nastavit pro každý sloupec na webu Azure Portal:

| Sloupec | Povolené hodnoty a jejich význam |
| --- | --- |
| Registrované údaje |**Alternativní e-mail**: Uživatel k ověření použil alternativní e-mail nebo ověřovací e-mail.<p><p>**Office phone**: Uživatel použil k ověření kancelářský telefon.<p>**Mobilní telefon**: Uživatel k ověření použil mobilní telefon nebo ověřovací telefon.<p>**Bezpečnostní otázky**: Uživatel k ověření použil bezpečnostní otázky.<p>**Jakákoli kombinace předchozích metod, například alternativní e-mail + mobilní telefon**: Nastane, když je zadána zásada dvou bran a ukazuje, které dvě metody uživatel použil k ověření svého požadavku na resetování hesla. |

## <a name="self-service-password-management-activity-types"></a>Typy aktivit správy samoobslužných hesel

V kategorii událostí auditu **samoobslužné správy hesel** se zobrazí následující typy aktivit:

* [Blokováno samoobslužným resetováním hesla](#activity-type-blocked-from-self-service-password-reset): Označuje, že se uživatel pokusil obnovit heslo, použít určitou bránu nebo ověřit telefonní číslo více než pětkrát za 24 hodin.
* [Změna hesla (samoobslužné)](#activity-type-change-password-self-service): Označuje, že uživatel provedl dobrovolnou nebo vynucenou (z důvodu vypršení platnosti) změnu hesla.
* [Resetování hesla (správcem):](#activity-type-reset-password-by-admin)Označuje, že správce provedl resetování hesla jménem uživatele z webu Azure Portal.
* [Resetování hesla (samoobslužné)](#activity-type-reset-password-self-service): Označuje, že uživatel úspěšně resetovat své heslo z [portálu pro resetování hesla Azure AD](https://passwordreset.microsoftonline.com).
* [Samoobslužné heslo obnovení aktivity toku](#activity-type-self-serve-password-reset-flow-activity-progress): Označuje každý konkrétní krok, který uživatel prochází, jako je například předání konkrétní brány pro resetování hesla ověřování, jako součást procesu resetování hesla.
* [Odemknout uživatelský účet (samoobslužný)](#activity-type-unlock-a-user-account-self-service)): Označuje, že uživatel úspěšně odemkl svůj účet služby Active Directory bez resetování hesla z [portálu pro resetování hesla služby Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory odemknutí účtu bez resetování.
* [Uživatel registrovaný pro samoobslužné resetování hesla](#activity-type-user-registered-for-self-service-password-reset): Označuje, že uživatel zaregistroval všechny požadované informace, aby mohl resetovat své heslo v souladu s aktuálně zadanou zásadou pro resetování hesla klienta.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ aktivity: Blokováno samoobslužným resetováním hesla

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že se uživatel pokusil obnovit heslo, použít určitou bránu nebo ověřit telefonní číslo více než pětkrát za 24 hodin.
* **Objekt actor aktivity**: Uživatel, který byl omezen z provádění dalších operací resetování. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který byl omezen z provádění dalších operací resetování. Uživatel může být koncový uživatel nebo správce.
* **Stav aktivity**:
  * _Úspěch_: Označuje, že uživatel byl omezen z provádění dalších resetování, pokusu o další metody ověřování nebo ověření dalších telefonních čísel pro příštích 24 hodin.
* **Důvod selhání stavu aktivity**: Nelze použít.

### <a name="activity-type-change-password-self-service"></a>Typ aktivity: Změna hesla (samoobslužné)

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že uživatel provedl dobrovolnou nebo vynucenou (z důvodu vypršení platnosti) změnu hesla.
* **Objekt actor aktivity**: Uživatel, který změnil své heslo. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který si změnil heslo. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Označuje, že uživatel úspěšně změnil své heslo.
  * _Selhání_: Označuje, že uživateli se nepodařilo změnit heslo. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.
* **Důvod selhání stavu aktivity**:
  * _FuzzyPolicyViolationInvalidPassword_: Uživatel vybral heslo, které bylo automaticky zakázáno, protože funkce microsoft banned password detection zjistil, že je příliš běžné nebo obzvláště slabé.

### <a name="activity-type-reset-password-by-admin"></a>Typ aktivity: Resetování hesla (podle správce)

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že správce provedl resetování hesla jménem uživatele z portálu Azure.
* **Objekt actor aktivity**: Správce, který provedl resetování hesla jménem jiného koncového uživatele nebo správce. Musí být správce hesel, správce uživatele nebo správce technické podpory.
* **Cíl aktivity**: Uživatel, jehož heslo bylo resetováno. Uživatel může být koncový uživatel nebo jiný správce.
* **Stavy aktivity**:
  * _Úspěch_: Označuje, že správce úspěšně resetoval heslo uživatele.
  * _Selhání_: Označuje, že se správci nepodařilo změnit heslo uživatele. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.

### <a name="activity-type-reset-password-self-service"></a>Typ aktivity: Resetování hesla (samoobslužné)

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že uživatel úspěšně obnovit své heslo z [portálu pro resetování hesla Azure AD](https://passwordreset.microsoftonline.com).
* **Objekt actor aktivity**: Uživatel, který resetuje své heslo. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který resetuje své heslo. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Označuje, že uživatel úspěšně obnovit své vlastní heslo.
  * _Selhání_: Označuje, že se uživateli nepodařilo obnovit vlastní heslo. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.
* **Důvod selhání stavu aktivity**:
  * _FuzzyPolicyViolationInvalidPassword_: Správce vybral heslo, které bylo automaticky zakázáno, protože funkce microsoft banned password detection zjistil, že je příliš běžné nebo obzvláště slabé.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ aktivity: Průběh aktivity toku resetování hesla

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje každý konkrétní krok, kterým uživatel prochází (například předání matné brány pro resetování hesla) jako součást procesu resetování hesla.
* **Objekt actor aktivity**: Uživatel, který provedl část toku resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který provedl část toku resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Označuje, že uživatel úspěšně dokončil určitý krok toku resetování hesla.
  * _Selhání_: Označuje, že se nezdařil konkrétní krok toku obnovení hesla. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.
* **Důvody stavu aktivity**: [Všechny důvody povoleného stavu aktivity obnovení](#description-of-the-report-columns-in-the-azure-portal)naleznete v následující tabulce .

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ aktivity: Odemknutí uživatelského účtu (samoobslužné)

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že uživatel úspěšně odemkl svůj účet služby Active Directory bez resetování hesla z [portálu pro resetování hesla služby Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory odemknutí účtu bez resetování.
* **Objekt actor aktivity**: Uživatel, který odemkl svůj účet bez resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který odemkl svůj účet bez resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Povolené stavy aktivit :**
  * _Úspěch_: Označuje, že uživatel úspěšně odemkl svůj vlastní účet.
  * _Selhání_: Označuje, že uživateli se nepodařilo odemknout svůj účet. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ aktivity: Uživatel registrovaný pro samoobslužné resetování hesla

Následující seznam podrobně vysvětluje tuto činnost:

* **Popis aktivity**: Označuje, že uživatel zaregistroval všechny požadované informace, aby bylo možné obnovit své heslo v souladu s aktuálně zadanou zásadou pro resetování hesla klienta. 
* **Objekt actor aktivity**: Uživatel, který se zaregistroval k obnovení hesla. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: Uživatel, který se zaregistroval k resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Povolené stavy aktivit :**
  * _Úspěch_: Označuje, že uživatel úspěšně zaregistroval pro resetování hesla v souladu s aktuální zásadou. 
  * _Selhání_: Označuje, že se uživateli nepodařilo zaregistrovat pro resetování hesla. Můžete vybrat řádek a zobrazit kategorii **Důvod stavu aktivity,** abyste se dozvěděli více o tom, proč k chybě došlo.

     >[!NOTE]
     >Selhání neznamená, že uživatel nemůže obnovit své vlastní heslo. To znamená, že nedokončili registrační proces. Pokud jsou v jejich účtu neověřená data, která jsou správná, například telefonní číslo, které není ověřeno, i když toto telefonní číslo neověřili, mohou je stále používat k resetování hesla.

## <a name="next-steps"></a>Další kroky

* [Vykazování využití služby SSPR a mfa a přehledů](howto-authentication-methods-usage-insights.md)
* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Zaregistrujte se pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md).
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Příklad protokolů auditu aktivity SSPR ve službě Azure AD"
