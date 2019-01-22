---
title: Samoobslužné resetování hesla sestavy – Azure Active Directory
description: Události vytváření sestav v Azure AD hesla pomocí samoobslužné služby obnovit.
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.openlocfilehash: a936626301c8e1a866817264342cc2c84130e158
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425906"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Možnosti zasílání zpráv o správě hesel Azure AD

Po nasazení, řada organizací chtít vědět, jak nebo pokud samoobslužné resetování hesla (SSPR) je doopravdy používá. Funkci vytváření sestav, které poskytuje Azure Active Directory (Azure AD) pomáhá lze zodpovědět otázky pomocí předem připravených sestav. Pokud máte správně licenci, můžete také vytvořit vlastní dotazy.

![Vytváření sestav][Reporting]

Sestavy, které existují v můžete odpovědět na následující otázky [webu Azure portal](https://portal.azure.com/):

> [!NOTE]
> Musí být [globálního správce](../users-groups-roles/directory-assign-admin-roles.md), a je nutné vyjádřit výslovný souhlas pro tato data shromáždit jménem svojí organizace. Chcete-li vyjádřit výslovný souhlas, musíte přejít **generování sestav** kartu nebo auditování protokoluje aspoň jednou. Dokud to neuděláte data nejsou shromažďována pro vaši organizaci.
>

* Kolik lidí zaregistrovali k resetování hesla?
* Kdo je zaregistrován pro resetování hesla?
* Jaká data se lidé registrace?
* Kolik lidí resetovat vlastní hesla v posledních sedmi dnů?
* Jaké jsou nejčastější metody, které uživatelé nebo správci použít k resetování hesel?
* Jaké jsou běžné problémy uživatelé nebo správci pro rozpoznávání tváře při pokusu o použití resetování hesla?
* Co správci jsou často resetování hesla?
* Existuje nějaké podezřelé aktivity dál resetování hesla?

## <a name="power-bi-content-pack"></a>Balíček obsahu Power BI

Pokud jste uživatel Power BI, je balíček obsahu Azure AD, která zahrnuje snadno použít vytváření sestav pro samoobslužné resetování HESLA. Další informace o tom, jak použít a nasadit balíček obsahu najdete v tématu [jak používat balíček obsahu Azure Active Directory Power BI](../reports-monitoring/howto-power-bi-content-pack.md). Díky balíčku obsahu můžete vytvořit vlastní řídicí panely a sdílet s ostatními uživateli ve vaší organizaci.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Postup zobrazení sestav správy hesel na webu Azure Portal

V Azure portal ještě neznáte vylepšili jsme způsobu, jakým se zobrazí resetování hesla a činnost registrace pro resetování hesla. Použijte následující, které kroky k nalezení heslo resetovat a událostí registrace pro resetování hesla:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v levém podokně.
3. Vyhledejte **Azure Active Directory** v seznamu služeb a vyberte ji.
4. Vyberte **Uživatelé a skupiny**.
5. Vyberte **protokoly auditu** z **uživatelů a skupin** nabídky. Zobrazí všechny události auditu, ke kterým došlo před všichni uživatelé ve vašem adresáři. Můžete filtrovat toto zobrazení můžete zobrazit všechny události související s hesly.
6. Chcete-li filtrovat toto zobrazení můžete zobrazit jenom události související s hesly obnovení, vyberte **filtr** tlačítko v horní části podokna.
7. Z **filtr** nabídce vyberte možnost **kategorie** rozevíracího seznamu a změňte ho na **samoobslužné správy hesel** typ kategorie.
8. Volitelně můžete dál filtrovat seznam výběrem konkrétní **aktivity** vás zajímá.

### <a name="converged-registration-preview"></a>Konvergované registrace (preview)

Pokud se účastníte ve verzi public preview sblížené registraci, najdete informace týkající se aktivity uživatele v protokolech auditování v kategorii **metody ověřování**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Popis sloupce sestavy na webu Azure Portal

Následující seznam popisuje všechny sloupce sestavy na webu Azure Portal podrobně:

* **Uživatel**: Uživatel, které se pokusily heslo resetovat operace Registrování.
* **Role**: Role uživatele v adresáři.
* **Datum a čas**: Datum a čas pokusu o.
* **Daty**: Ověřovací data, že registrace pro resetování hesla zadaného uživatele.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Popis hodnoty sestavy na webu Azure Portal

Následující tabulka popisuje různé hodnoty, které jsou že pro každý sloupec na webu Azure Portal můžete nastavit:

| Sloupec | Povolené hodnoty a jejich význam |
| --- | --- |
| Daty |**Alternativní e-mailu**: Uživatel používá k ověření alternativní e-mailu nebo e-mail pro ověření.<p><p>**Telefon do kanceláře**: Uživatel používá k ověření pomocí telefonu v kanceláři.<p>**Mobilní telefon**: Uživatel použil mobilní telefon nebo ověřovací telefon pro ověření.<p>**Bezpečnostní otázky**: Uživatel používá k ověření bezpečnostních otázek.<p>**Libovolnou kombinaci výše uvedených metod, například alternativní e-mail + mobilního telefonu**: Nastane, pokud zásady dvě brány je zadána a ukazuje, které dvě metody uživatel použitý k ověření žádost o resetování hesla. |

## <a name="self-service-password-management-activity-types"></a>Typy aktivit samoobslužné správy hesel

Následující typy aktivit joinkind **samoobslužné správy hesel** kategorii událostí auditu:

* [Blokování resetování hesla pomocí samoobslužné služby](#activity-type-blocked-from-self-service-password-reset): Označuje, že se uživatel pokusil resetování hesla, pomocí konkrétní brány nebo ověřit telefonní číslo více než pět celkové doby za 24 hodin.
* [Změna hesla (Samoobslužná)](#activity-type-change-password-self-service): Označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změnu hesla.
* [Resetování hesla (správcem)](#activity-type-reset-password-by-admin): Označuje, že správce provedena heslo resetovat jménem uživatele z portálu Azure portal.
* [Resetování hesla (Samoobslužná)](#activity-type-reset-password-self-service): Označuje, že uživatel úspěšně resetovat heslo [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* [Průběh aktivity toku samoobslužného resetování](#activity-type-self-serve-password-reset-flow-activity-progress): Každý konkrétní krok, který uživatel pokračuje přes, označuje, jako je ověřování brány předá určité heslo resetovat, protože část hesla proces obnovení.
* [Odemknutí uživatelského účtu (samoobslužné)](#activity-type-unlock-user-account-self-service): Označuje, že uživatel úspěšně odemknuli svůj účet služby Active Directory bez resetování hesel z [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory účet odemknout bez resetování.
* [Uživatel zaregistrovaný pro samoobslužné resetování hesla](#activity-type-user-registered-for-self-service-password-reset): Označuje, že uživatel zaregistroval všechny požadované informace pro také moct resetovat své heslo v souladu s zásady pro resetování hesla nyní zadaného klienta.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ aktivity: Blokování samoobslužného resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že se uživatel pokusil resetování hesla, pomocí konkrétní brány nebo ověřit telefonní číslo více než pět celkové doby za 24 hodin.
* **Aktivita actor**: Uživatel, který byla omezena z provedením další operace resetování. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatel, který byla omezena z provedením další operace resetování. Tímto uživatelem může být koncový uživatel nebo správce.
* **Stav aktivity**:
  * _Úspěch_: Označuje, že uživatel byla omezena z provádění jakékoli další resetování, pokusu o jakékoli další metody ověřování nebo ověřování jakékoli další telefonní čísla dobu následujících 24 hodin.
* **Důvod selhání stavu aktivity**: Není k dispozici.

### <a name="activity-type-change-password-self-service"></a>Typ aktivity: Změna hesla (samoobslužná)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změnu hesla.
* **Aktivita actor**: Uživatel, který změní heslo. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatel, který změní heslo. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Označuje, že uživatel se úspěšně změnil heslo.
  * _Selhání_: Udává, že uživatel se nepodařilo změnit své heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: Uživatel vybral, který byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zvlášť slabé heslo.

### <a name="activity-type-reset-password-by-admin"></a>Typ aktivity: Resetování hesla (správcem)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že správce provedena heslo resetovat jménem uživatele z portálu Azure portal.
* **Aktivita actor**: Správce, který provedl resetování jménem jiného koncový uživatel nebo správce hesel. Musí být buď globální správce, heslo správce, Správce uživatelů nebo správce technické podpory.
* **Cílové aktivity**: Uživatel, jehož heslo se resetovalo. Tímto uživatelem může být koncový uživatel nebo jiný správce.
* **Aktivita stavy**:
  * _Úspěch_: Označuje, že správce bylo úspěšně resetováno heslo uživatele.
  * _Selhání_: Udává, že správce se nepodařilo změnit heslo uživatele. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-reset-password-self-service"></a>Typ aktivity: Resetování hesla (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že uživatel úspěšně resetovat heslo [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* **Aktivita actor**: Uživatel, který resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatel, který resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Označuje, že uživatel úspěšně resetoval svoje vlastní heslo.
  * _Selhání_: Udává, že uživatel se nepodařilo resetovat vlastní heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: Vybrat správce, který byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zvlášť slabé heslo.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ aktivity: Průběh aktivity toku pro resetování hesla svým slouží

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Každý konkrétní krok, který uživatel pokračuje přes (např. předání určité heslo resetovat bránu ověřování) označuje jako součást heslo resetovat procesu.
* **Aktivita actor**: Uživatele, který provedl součástí heslo resetovat toku. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatele, který provedl součástí heslo resetovat toku. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Indikuje, že uživatel úspěšně dokončeno konkrétním krokem procesu resetování hesla.
  * _Selhání_: Určuje, že konkrétní krok hesla resetovat tok selhal. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvody stavu aktivity**:   V následující tabulce najdete [všechny důvody přípustné obnovení činnosti stavu](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ aktivity: Odemknutí uživatelského účtu (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že uživatel úspěšně odemknuli svůj účet služby Active Directory bez resetování hesel z [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory účet odemknout bez resetování.
* **Aktivita actor**: Uživatel, který odemknout svůj účet bez resetování hesel. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatel, který odemknout svůj účet bez resetování hesel. Tímto uživatelem může být koncový uživatel nebo správce.
* **Povolené stavy aktivity**:
  * _Úspěch_: Označuje, že uživatel úspěšně odemknuli svůj vlastní účet.
  * _Selhání_: Udává, že uživatel se nepodařilo odemknout svůj účet. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ aktivity: Registrace uživatele pro samoobslužné resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Označuje, že uživatel zaregistroval všechny požadované informace pro také moct resetovat své heslo v souladu s zásady pro resetování hesla nyní zadaného klienta. 
* **Aktivita actor**: Uživatel, který je registrovaný pro resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: Uživatel, který je registrovaný pro resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Povolené stavy aktivity**:
  * _Úspěch_: Označuje, že uživatel úspěšně zaregistrován v souladu s aktuální zásady resetování hesla. 
  * _Selhání_: Označuje, že se nepodařilo zaregistrovat pro resetování hesla uživatele. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě. 

     >[!NOTE]
     >Selhání neznamená, že uživatel se nepovedlo se resetovat vlastní heslo. Znamená to, že se nedokončilo proces registrace. Pokud neověřené data na svůj účet, který je správný, jako je například telefonní číslo, který není ověřen, i v případě, že neověřili toto telefonní číslo, že ji mohou dál používat k resetování hesla. Další informace najdete v tématu [co se stane, když se uživatel zaregistruje?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Protokoly příklad aktivit auditu samoobslužného resetování HESLA ve službě Azure AD"
