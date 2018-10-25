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
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 7d7de02c96b9a17144b446b9ed86ebcbacd51162
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025001"
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

* **Uživatel**: uživatel, které se pokusily heslo resetovat operace Registrování.
* **Role**: role uživatele v adresáři.
* **Datum a čas**: datum a čas pokusu o.
* **Registrovaná data**: ověřovací data, že registrace pro resetování hesla zadaného uživatele.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Popis hodnoty sestavy na webu Azure Portal

Následující tabulka popisuje různé hodnoty, které jsou že pro každý sloupec na webu Azure Portal můžete nastavit:

| Sloupec | Povolené hodnoty a jejich význam |
| --- | --- |
| Daty |**Alternativní e-mailu**: uživatel používá k ověření alternativní e-mailu nebo e-mail pro ověření.<p><p>**Telefon do kanceláře**: uživatel používá k ověření pomocí telefonu v kanceláři.<p>**Mobilní telefon**: uživatel použil mobilní telefon nebo ověřovací telefon pro ověření.<p>**Bezpečnostní otázky**: uživatel používá k ověření bezpečnostních otázek.<p>**Libovolnou kombinaci výše uvedených metod, například alternativní e-mail + mobilního telefonu**: Vyvolá se v případě zásady dvě brány je zadán a ukazuje, které dvě metody uživatele, používá se k ověřování žádost o resetování hesla. |

## <a name="self-service-password-management-activity-types"></a>Typy aktivit samoobslužné správy hesel

Následující typy aktivit joinkind **samoobslužné správy hesel** kategorii událostí auditu:

* [Blokování resetování hesla pomocí samoobslužné služby](#activity-type-blocked-from-self-service-password-reset): Určuje, že se uživatel pokusil resetování hesla, pomocí konkrétní brány nebo ověřit telefonní číslo více než pět celkové doby za 24 hodin.
* [Změna hesla (Samoobslužná)](#activity-type-change-password-self-service): označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změnu hesla.
* [Resetování hesla (správcem)](#activity-type-reset-password-by-admin): Určuje, že provádí správce jménem uživatele z portálu Azure portal resetování hesla.
* [Resetování hesla (Samoobslužná)](#activity-type-reset-password-self-service): Určuje, že uživatel úspěšně resetovat heslo [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* [Průběh aktivity toku samoobslužného resetování](#activity-type-self-serve-password-reset-flow-activity-progress): označuje každý konkrétní krok uživatel pokračuje přes, jako je ověřování brány předá určité heslo resetovat, protože část hesla proces obnovení.
* [Odemknutí uživatelského účtu (samoobslužné)](#activity-type-unlock-user-account-self-service): Určuje, že uživatel úspěšně odemknuli svůj účet služby Active Directory bez resetování hesel z [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí aktivní Funkce adresáře účtu odemknout bez resetování.
* [Uživatel zaregistrovaný pro samoobslužné resetování hesla](#activity-type-user-registered-for-self-service-password-reset): Určuje, že uživatel zaregistroval všechny požadované informace pro také moct resetovat své heslo v souladu s zásady pro resetování hesla nyní zadaného klienta.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ aktivity: blokování resetování hesla pomocí samoobslužné služby

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že se uživatel pokusil resetování hesla, pomocí konkrétní brány nebo ověřit telefonní číslo více než pět celkové doby za 24 hodin.
* **Aktivita actor**: uživatel, který byla omezena z provedením další operace resetování. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: uživatel, který byla omezena z provedením další operace resetování. Tímto uživatelem může být koncový uživatel nebo správce.
* **Stav aktivity**:
  * _Úspěch_: Určuje, že uživatel byla omezena z provádění jakékoli další resetování, pokusu o jakékoli další metody ověřování nebo ověřování jakékoli další telefonní čísla dobu následujících 24 hodin.
* **Důvod selhání stavu aktivity**: není k dispozici.

### <a name="activity-type-change-password-self-service"></a>Typ aktivity: Změna hesla (Samoobslužná)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změnu hesla.
* **Aktivita actor**: uživatel, který změní heslo. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: uživatel, který změní heslo. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Určuje, že uživatel se úspěšně změnil heslo.
  * _Selhání_: Určuje, že uživatel se nepodařilo změnit své heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: uživatel vybral, který byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zvlášť slabé heslo.

### <a name="activity-type-reset-password-by-admin"></a>Typ aktivity: resetování hesla (správcem)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že provádí správce jménem uživatele z portálu Azure portal resetování hesla.
* **Aktivita actor**: správce, který provedl resetování jménem jiného koncový uživatel nebo správce hesel. Musí být buď globální správce, heslo správce, Správce uživatelů nebo správce technické podpory.
* **Cílové aktivity**: uživatele, jejichž heslo se resetovalo. Tímto uživatelem může být koncový uživatel nebo jiný správce.
* **Aktivita stavy**:
  * _Úspěch_: Určuje, že správce úspěšně resetovat heslo uživatele.
  * _Selhání_: Určuje, že správce se nepodařilo změnit heslo uživatele. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-reset-password-self-service"></a>Typ aktivity: resetování hesla (Samoobslužná)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel úspěšně resetovat heslo [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* **Aktivita actor**: resetování hesla uživatele. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: resetování hesla uživatele. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Určuje, že uživatel úspěšně resetovat vlastní heslo.
  * _Selhání_: Určuje, že uživatel se nepovedlo resetovat vlastní heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: Vybraný správce, který byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zvlášť slabé heslo.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ aktivity: Self sloužit průběh aktivity toku resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: označuje každý konkrétní krok uživatel pokračuje přes (např. předání určité heslo resetovat bránu ověřování) jako součást heslo resetovat procesu.
* **Aktivita actor**: uživatele, který provedl součástí heslo resetovat toku. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: uživatele, který provedl součástí heslo resetovat toku. Tímto uživatelem může být koncový uživatel nebo správce.
* **Aktivita stavy**:
  * _Úspěch_: Určuje, že uživatel úspěšně dokončen konkrétním krokem procesu resetování hesla.
  * _Selhání_: Určuje, že konkrétní krok hesla resetovat tok selhal. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvody stavu aktivity**: najdete v následující tabulce [všechny důvody přípustné obnovení činnosti stavu](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ aktivity: odemknutí uživatelského účtu (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel úspěšně odemknuli svůj účet služby Active Directory bez resetování hesel z [portál pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory odemknutí účtu bez resetování.
* **Aktivita actor**: uživatel, který odemknout svůj účet bez resetování hesel. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: uživatel, který odemknout svůj účet bez resetování hesel. Tímto uživatelem může být koncový uživatel nebo správce.
* **Povolené stavy aktivity**:
  * _Úspěch_: Určuje, že uživatel úspěšně odemknuli svůj vlastní účet.
  * _Selhání_: Určuje, že uživatel se nepodařilo odemknout svůj účet. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ aktivity: uživatel zaregistrovaný pro samoobslužné resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel zaregistroval všechny požadované informace pro také moct resetovat své heslo v souladu s zásady pro resetování hesla nyní zadaného klienta. 
* **Aktivita actor**: uživatel zaregistrovaný pro resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Cílové aktivity**: uživatel zaregistrovaný pro resetování hesla. Tímto uživatelem může být koncový uživatel nebo správce.
* **Povolené stavy aktivity**:
  * _Úspěch_: Určuje, že uživatel úspěšně zaregistrované v souladu s aktuální zásady resetování hesla. 
  * _Selhání_: Určuje, že uživatel se nepodařilo zaregistrovat pro resetování hesla. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě. 

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
