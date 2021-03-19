---
title: Sestavy samoobslužného resetování hesla – Azure Active Directory
description: Vytváření sestav pro události samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579174"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Možnosti vytváření sestav správy hesel v Azure AD

Po nasazení mnoho organizací chce zjistit, jak nebo jestli se ve skutečnosti používá Samoobslužné resetování hesla (SSPR). Funkce vytváření sestav, kterou Azure Active Directory (Azure AD), vám pomůže odpovědět na otázky pomocí předem vytvořených sestav. Pokud máte patřičnou licenci, můžete také vytvořit vlastní dotazy.

![Vytváření sestav o SSPR pomocí protokolů auditu v Azure AD][Reporting]

Následující dotazy mohou být zodpovězené sestavami, které existují v [Azure Portal](https://portal.azure.com/):

> [!NOTE]
> Musíte být [globálním správcem](../roles/permissions-reference.md)a musíte se vyjádřit, že se tato data budou shromažďovat jménem vaší organizace. Chcete-li se vyjádřit výslovný souhlas, musíte navštívit kartu pro **vytváření sestav** nebo protokoly auditu aspoň jednou. Do té doby nebudou pro vaši organizaci shromažďována data.
>

* Kolik lidí zaregistrovalo pro resetování hesla?
* Kdo zaregistroval pro resetování hesla?
* Jaká data uživatelé registrují?
* Kolik lidí obnovilo hesla během posledních sedmi dnů?
* Jaké jsou nejběžnější metody, které uživatelé nebo Správci používají k resetování hesel?
* Co jsou běžné problémy, které uživatelé nebo správci čelí při pokusu o použití resetování hesla?
* Co správci často resetují svoje vlastní hesla?
* Dochází k nějakým podezřelým aktivitám při resetování hesla?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Zobrazení sestav správy hesel na webu Azure Portal

V prostředí Azure Portal jsme vylepšili způsob, jakým můžete zobrazit aktivitu resetování hesla a resetování hesla. Pomocí následujícího postupu můžete najít události pro resetování hesla a resetování hesla:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. V levém podokně vyberte **Všechny služby**.
3. V seznamu služeb vyhledejte **Azure Active Directory** a vyberte ji.
4. V části Správa vyberte **Uživatelé** .
5. V okně **Uživatelé** vyberte **protokoly auditu** . Zobrazí se všechny události auditu, ke kterým došlo u všech uživatelů v adresáři. V tomto zobrazení můžete filtrovat, aby se zobrazily všechny události související s heslem.
6. V nabídce **filtru** v horní části podokna vyberte rozevírací seznam **Služba** a změňte ji na typ služby **Samoobslužná správa hesel** .
7. Volitelně můžete seznam filtrovat podle konkrétní **aktivity** , na kterou vás zajímáte.

### <a name="combined-registration"></a>Kombinovaná registrace

Pokud jste povolili [kombinovanou registraci](./concept-registration-mfa-sspr-combined.md), budou se v části   >  **metody ověřování** zabezpečení najít informace týkající se aktivity uživatele v protokolech auditu.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Popis sloupců sestavy v Azure Portal

Následující seznam obsahuje podrobné vysvětlení každého sloupce sestavy v Azure Portal:

* **Uživatel**: uživatel, který se pokusil o operaci registrace resetování hesla.
* **Role**: role uživatele v adresáři.
* **Datum a čas**: datum a čas pokusu.
* **Registrovaná data**: ověřovací data, která uživatel zadal během registrace resetování hesla.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Popis hodnot sestavy v Azure Portal

Následující tabulka popisuje různé hodnoty, které můžete nastavit pro každý sloupec v Azure Portal:

| Sloupec | Povolené hodnoty a jejich významy |
| --- | --- |
| Zaregistrovaná data |**Alternativní e-mail**: uživatel použil k ověření alternativní e-mail nebo ověřovací e-mail.<p><p>**Telefon do kanceláře**: uživatel pro ověření použil telefon do kanceláře.<p>**Mobilní telefon**: uživatel používal mobilní telefon nebo telefon pro ověření k ověření.<p>**Bezpečnostní otázky**: uživatel použil bezpečnostní otázky k ověření.<p>**Libovolná kombinace předchozích metod, například alternativní e-mailová + mobilní telefon**: nastane, když je zadána zásada dvou bran, a ukazuje, které dvě metody uživatel použil k ověřování své žádosti o resetování hesla. |

## <a name="self-service-password-management-activity-types"></a>Self-Service typy aktivit správy hesel

V kategorii událostí auditu **Samoobslužná správa hesel** se zobrazí následující typy aktivit:

* [Blokování samoobslužného resetování hesla](#activity-type-blocked-from-self-service-password-reset): označuje, že se uživatel pokusil resetovat heslo, používat konkrétní bránu nebo ověřit telefonní číslo více než pět celkových časů za 24 hodin.
* [Změna hesla (Samoobslužná služba)](#activity-type-change-password-self-service): označuje, že uživatel provedl dobrovolné nebo vynucené změně hesla (z důvodu vypršení platnosti).
* [Resetování hesla (správcem)](#activity-type-reset-password-by-admin): označuje, že správce provedl resetování hesla jménem uživatele z Azure Portal.
* [Resetování hesla (Samoobslužná služba)](#activity-type-reset-password-self-service): označuje, že uživatel úspěšně obnovil heslo z [portálu pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* [Průběh aktivity toku samoobslužného resetování hesla](#activity-type-self-serve-password-reset-flow-activity-progress): označuje každý konkrétní krok, pomocí kterého uživatel pokračuje, jako je například předání konkrétní ověřovací brány pro resetování hesla, jako součást procesu resetování hesla.
* [Odemknutí uživatelského účtu (samoobslužné)](#activity-type-unlock-a-user-account-self-service)): označuje, že uživatel úspěšně odemkl svůj účet Active Directory bez resetování hesla z portálu pro [resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce Active Directory pro odemknutí účtu bez resetování.
* [Uživatel zaregistrovaný pro Samoobslužné resetování hesla](#activity-type-user-registered-for-self-service-password-reset): označuje, že uživatel zaregistroval všechny požadované informace, aby bylo možné resetovat heslo v souladu s aktuálně zadanými zásadami pro resetování hesla tenanta.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ aktivity: blokováno z samoobslužného resetování hesla

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že uživatel se pokusil resetovat heslo, používat konkrétní bránu nebo ověřit telefonní číslo více než pět celkových časů za 24 hodin.
* **Objekt actor aktivity**: uživatel, který byl omezen prováděním dalších operací resetování. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který byl omezen prováděním dalších operací resetování. Uživatel může být koncovým uživatelem nebo správcem.
* **Stav aktivity**:
  * _Úspěch_: označuje, že se uživatel omezil při provádění jakýchkoli dalších resetů, pokus o jakékoli další metody ověřování nebo za dalších 24 hodin za účelem ověření dalších telefonních čísel.
* **Důvod selhání stavu aktivity**: nelze použít.

### <a name="activity-type-change-password-self-service"></a>Typ aktivity: změnit heslo (Samoobslužná služba)

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že uživatel provedl dobrovolné nebo vynucené změně hesla (z důvodu vypršení platnosti).
* **Objekt actor aktivity**: uživatel, který změnil heslo. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který změnil heslo. Uživatel může být koncovým uživatelem nebo správcem.
* **Stavy aktivity**:
  * _Úspěch_: označuje, že uživatel úspěšně změnil heslo.
  * _Selhání_: označuje, že se uživateli nepodařilo změnit heslo. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.
* **Důvod selhání stavu aktivity**:
  * _FuzzyPolicyViolationInvalidPassword_: uživatel vybral heslo, které se automaticky zakázalo, protože možnosti detekce hesla zakázané společností Microsoft zjistily, že jsou příliš běžné nebo obzvláště slabé.

### <a name="activity-type-reset-password-by-admin"></a>Typ aktivity: resetování hesla (správcem)

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že správce provedl resetování hesla jménem uživatele z Azure Portal.
* **Objekt actor aktivity**: správce, který provedl resetování hesla jménem jiného koncového uživatele nebo správce. Musíte být správcem hesla, správcem uživatele nebo správcem helpdesku.
* **Cíl aktivity**: uživatel, jehož heslo bylo resetováno. Uživatel může být koncovým uživatelem nebo jiným správcem.
* **Stavy aktivity**:
  * _Úspěch_: označuje, že správce úspěšně obnovil heslo uživatele.
  * _Selhání_: označuje, že správci se nepodařilo změnit heslo uživatele. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.

### <a name="activity-type-reset-password-self-service"></a>Typ aktivity: resetování hesla (Samoobslužná služba)

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že uživatel úspěšně obnovil heslo z portálu pro [resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* **Objekt actor aktivity**: uživatel, který obnovil heslo. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který obnovil heslo. Uživatel může být koncovým uživatelem nebo správcem.
* **Stavy aktivity**:
  * _Úspěch_: označuje, že uživatel úspěšně obnovil vlastní heslo.
  * _Selhání_: označuje, že se uživateli nepodařilo resetovat vlastní heslo. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.
* **Důvod selhání stavu aktivity**:
  * _FuzzyPolicyViolationInvalidPassword_: správce vybral heslo, které je automaticky zakázané, protože možnosti detekce hesla zakázané Microsoftem byly zjištěny příliš běžné nebo obzvláště slabé.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ aktivity: samotný postup aktivity toku resetování hesla

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje každý konkrétní krok, který uživatel v rámci procesu resetování hesla prochází (například předání konkrétní ověřovací brány pro resetování hesla).
* **Objekt actor aktivity**: uživatel, který provedl část toku resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který provedl část toku resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Stavy aktivity**:
  * _Úspěch_: označuje, že uživatel úspěšně dokončil konkrétní krok toku resetování hesla.
  * _Selhání_: označuje, že se nezdařil konkrétní krok toku resetování hesla. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.
* **Důvody aktivity**: pro [všechny přípustné důvody stavu aktivity resetování](#description-of-the-report-columns-in-the-azure-portal)se podívejte na následující tabulku.

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ aktivity: odemknutí uživatelského účtu (Samoobslužná služba)

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že uživatel úspěšně odemkl svůj účet Active Directory bez resetování hesla z portálu pro [resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce Active Directory pro odemknutí účtu bez resetování.
* **Objekt actor aktivity**: uživatel, který účet odemkl, bez resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který účet odemkl, bez resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Povolené stavy aktivity**:
  * _Úspěch_: indikuje, že uživatel úspěšně odemkl svůj vlastní účet.
  * _Chyba_: označuje, že uživatel nedokázal odemknout svůj účet. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ aktivity: Uživatel zaregistrovaný pro Samoobslužné resetování hesla

Následující seznam vysvětluje tuto aktivitu podrobněji:

* **Popis aktivity**: označuje, že uživatel zaregistroval všechny požadované informace, aby bylo možné resetovat heslo v souladu s aktuálně zadanými zásadami pro resetování hesla tenanta. 
* **Objekt actor aktivity**: uživatel, který byl zaregistrován pro resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Cíl aktivity**: uživatel, který zaregistroval pro resetování hesla. Uživatel může být koncovým uživatelem nebo správcem.
* **Povolené stavy aktivity**:
  * _Úspěch_: označuje, že se uživatel úspěšně zaregistroval pro resetování hesla v souladu s aktuálními zásadami. 
  * _Chyba_: označuje, že se uživateli nepodařilo zaregistrovat k resetování hesla. Výběrem řádku můžete zobrazit kategorii **Důvod stavu aktivity** a získat další informace o tom, proč k chybě došlo.

     >[!NOTE]
     >Selhání neznamená, že uživatel nemůže resetovat vlastní heslo. To znamená, že nedokončili proces registrace. Pokud jsou na svém účtu neověřená data, jako je třeba telefonní číslo, které se neověřuje, i když si toto telefonní číslo neověřili, můžou ho i nadále použít k resetování hesla.

## <a name="next-steps"></a>Další kroky

* [Vytváření sestav využití SSPR a MFA a přehledy](./howto-authentication-methods-activity.md)
* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Zaregistrujte se na Samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md).
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](./tutorial-enable-sspr-writeback.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](./troubleshoot-sspr.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Příklad protokolů auditu aktivity SSPR v Azure AD"