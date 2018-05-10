---
title: Resetování hesla pomocí samoobslužné služby sestavy – Azure Active Directory
description: Zprávy o hesla pomocí samoobslužné služby Azure AD resetovat události
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 45183e51e9071647e07cfd0738522f0dfa28bd7b
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Možnosti zasílání zpráv o správou hesel Azure AD

Po nasazení mnoho organizací chtít vědět, jak nebo pokud samoobslužného obnovení hesla (SSPR) je doopravdy používá. Funkci generování sestav, které poskytuje Azure Active Directory (Azure AD) lze zodpovědět otázky pomocí předem sestav. Pokud jste správně licenci, můžete také vytvořit vlastní dotazy.

![Vytváření sestav][Reporting]

Sestavy, které existují v [portál Azure] (může odpovědět na následující otázkyhttps://portal.azure.com/):

> [!NOTE]
> Musí být [globální správce](../active-directory-assign-admin-roles-azure-portal.md), a je nutné výslovný souhlas pro tato data shromáždit jménem vaší organizace. Se vyjádřit výslovný souhlas, musí navštívíte **Reporting** kartě nebo auditu protokoly alespoň jednou. Do té doby se data nejsou shromažďovány pro vaši organizaci.
>

* Kolik lidí registrovali pro resetování hesla?
* Kdo má zaregistrovat pro resetování hesla?
* Jaká data jsou osoby registraci?
* Kolik uživatelů resetovat vlastní hesla v posledních sedmi dnů?
* Jaké jsou nejčastější metody, které uživatelé nebo správci použít k vytvoření nového hesla?
* Jaké jsou běžné problémy uživatelé nebo správci setkávají při pokusu o použití resetování hesla?
* Co správci jsou často resetovat vlastní hesla?
* Je k dispozici podezřelé aktivity přejdete k resetování hesla?

## <a name="power-bi-content-pack"></a>Balíček obsahu Power BI

Pokud si uživatel Power BI, je pro Azure AD, která zahrnuje snadno použitelné vytváření sestav pro SSPR balíček obsahu. Další informace o tom, jak používat a nasaďte balíček obsahu najdete v tématu [jak používat Azure Active Directory Power BI. balíček obsahu](../active-directory-reporting-power-bi-content-pack-how-to.md). Balíček obsahu můžete vytvořit vlastní řídicí panely a sdílet je s ostatními uživateli ve vaší organizaci.

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Postup zobrazení sestav správy hesel na portálu Azure

V portálu Azure prostředí jsme vylepšili způsobu, jakým resetování hesla můžete zobrazit a registrace aktivita resetování hesla. Použijte následující postup najít heslo resetováno a události registrace pro resetování hesla:

1. Vyhledejte [portál Azure](https://portal.azure.com).
2. Vyberte **všechny služby** v levém podokně.
3. Vyhledejte **Azure Active Directory** v seznamu služeb a vyberte ho.
4. Vyberte **uživatelů a skupin**.
5. Vyberte **protokoly auditu** z **uživatelů a skupin** nabídky. Toto jsou zobrazeny všechny událostí auditu, které pro všechny uživatele ve vašem adresáři došlo k chybě. Toto zobrazení zobrazíte všechny události související s hesly můžete filtrovat.
6. Chcete-li filtrovat toto zobrazení zobrazí se pouze události související s hesly resetování, vyberte **filtru** tlačítka v horní části podokna.
7. Z **filtru** nabídce vyberte možnost **kategorie** rozevírací seznam a změňte ji na **Samoobslužná správa hesel** typ kategorie.
8. Volitelně můžete dál filtrovat seznam výběrem konkrétní **aktivity** vás zajímá.

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Jak načíst heslo správy události z sestav Azure AD a události rozhraní API

Sestavy služby Azure AD a události rozhraní API podporuje načtení všechny informace, které jsou součástí resetování hesla a zprávy o registraci pro resetování hesla. Když použijete toto rozhraní API, můžete stáhnout události registrace resetování jednotlivých heslo pro resetování a heslo a integrovat technologie vytváření sestav podle svého výběru.

> [!IMPORTANT]
> V současné době sestav Azure AD a rozhraní API událostí načte až *než 75 000 jednotlivé události* z [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) a [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) typy. Rozhraní API zahrnuje za posledních 30 dní.
> 
> Pokud budete potřebovat načíst nebo ukládání dat nad rámec tohoto okna, doporučujeme ho uchování v externí databáze pomocí rozhraní API k dotazování rozdílů, kterých se. Doporučujeme vám, že začnete tato data načíst, když spustíte pomocí SSPR ve vaší organizaci. Zachovat ho externě a poté pokračujte ke sledování se rozdíly od tohoto okamžiku.
>

### <a name="how-to-get-started-with-the-reporting-api"></a>Jak začít pracovat s rozhraním API pro generování sestav

Pro přístup k těmto datům, budete muset zápisu malých aplikace nebo skriptu, který ji načíst z našich serverech. Další informace najdete v tématu [Začínáme s Azure AD reporting rozhraní API](../active-directory-reporting-api-getting-started-azure-portal.md).

Až budete mít skript práci, budete chtít Zkontrolujte heslo resetování a registraci události, které můžete načíst ke splnění scénářů:

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): seznam sloupců, které jsou k dispozici pro resetování hesla události.
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): seznam sloupců, které jsou k dispozici pro události registrace pro resetování hesla.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Popis sloupce sestavy na portálu Azure

Následující seznam popisuje jednotlivé sloupce sestavy na portálu Azure podrobně:

* **Uživatel**: uživatel, který se pokusil heslo resetovat operace Registrování.
* **Role**: roli uživatele v adresáři.
* **Datum a čas**: datum a čas pokus.
* **Data registrovaná**: ověřování dat, že registrace pro resetování zadaného během heslo uživatele.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Popis hodnoty sestavy na portálu Azure

Následující tabulka popisuje různé hodnoty, které jsou že pro každý sloupec na portálu Azure můžete nastavit:

| Sloupec | Povolené hodnoty a jejich významů |
| --- | --- |
| Data zaregistrován |**Alternativní e-mailu**: uživatel používá alternativní e-mail nebo e-mailu ověřování k ověření.<p><p>**Telefon do kanceláře**: uživatel použít k ověřování telefonní číslo do kanceláře.<p>**Mobilní telefon**: uživatel používá k ověření mobilního telefonu nebo telefon pro ověření.<p>**Bezpečnostní otázky**: uživatel používá k ověření bezpečnostních otázek.<p>**Libovolnou kombinaci předchozích metod, například alternativní e-mailu + mobilního telefonu**: nastane, když je zadán zásadu dvě brány a ukazuje které dvě metody uživatel použitý k ověření žádost o resetování hesla. |

## <a name="self-service-password-management-activity-types"></a>Samoobslužné služby typy aktivit správy hesel

Následující typy aktivit v zobrazí **Samoobslužná správa hesel** kategorie události auditu:

* [Blokovat samoobslužné resetování hesla](#activity-type-blocked-from-self-service-password-reset): Určuje, že uživatel byl proveden pokus o resetování hesla, použijte bránu konkrétní nebo ověřit telefonní číslo více než pět celkové časy za 24 hodin.
* [Změnit heslo (samoobslužné)](#activity-type-change-password-self-service): označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změna hesla.
* [Resetovat heslo (správcem)](#activity-type-reset-password-by-admin): znamená, že správce provedena heslo resetovat jménem uživatele z portálu Azure.
* [Resetovat heslo (samoobslužné)](#activity-type-reset-password-self-service): Určuje, že uživatel úspěšně resetovat heslo služby [portálu pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* [Resetování hesla pomocí samoobslužné služby průběh aktivity toku](#activity-type-self-serve-password-reset-flow-activity-progress): označuje každou konkrétní krok uživatele pokračuje prostřednictvím, například předávání konkrétní heslo resetovat bránu ověřování, jako součást heslo proces obnovení.
* [Odemknutí uživatelský účet (samoobslužné)](#activity-type-unlock-user-account-self-service): Určuje, že uživatel účtu služby Active Directory byla úspěšně odemknuta bez resetování hesla z [portálu pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí aktivní Funkce Directory účtu odemknout bez resetování.
* [Uživatel zaregistrovat pro resetování hesla pomocí samoobslužné služby](#activity-type-user-registered-for-self-service-password-reset): Určuje, že uživatel má zaregistrované všechny požadované informace, abyste mohli obnovit své heslo v souladu s zásady resetování hesel aktuálně zadaného klienta.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Typ aktivity: blokovat samoobslužné resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel byl proveden pokus o resetování hesla, použijte bránu konkrétní nebo ověřit telefonní číslo více než pět celkové časy za 24 hodin.
* **Aktivita objektu actor**: uživatel, který byl omezeny provedení další operace resetování. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který byl omezeny provedení další operace resetování. Uživatel může být koncový uživatel nebo správce.
* **Stav aktivity**:
  * _Úspěch_: Určuje, že byl uživatel omezeny z provádět žádné další nastavení, pokus žádné další metody ověřování nebo ověřování žádné další telefonní čísla pro dalších 24 hodin.
* **Důvod selhání stavu aktivity**: není k dispozici.

### <a name="activity-type-change-password-self-service"></a>Typ aktivity: Změna hesla (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: označuje, že uživatel provést dobrovolná nebo vynutit (z důvodu vypršení platnosti) změna hesla.
* **Aktivita objektu actor**: uživatel, který změnit své heslo. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který změnit své heslo. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Určuje, že uživatel úspěšně změnit své heslo.
  * _Selhání_: Určuje, že uživatel se nepodařilo změnit své heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: uživatel vybral heslo, které byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zejména slabé.

### <a name="activity-type-reset-password-by-admin"></a>Typ aktivity: resetování hesla (správcem)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: znamená, že správce provedena heslo resetovat jménem uživatele z portálu Azure.
* **Aktivita objektu actor**: správce, který provedl resetování jménem jiného koncový uživatel nebo správce hesel. Musí být buď globální správce, heslo správce, Správce uživatelů nebo správce technické podpory.
* **Cíl aktivity**: uživatel, jehož heslo byl resetován. Uživatel může být koncový uživatel nebo jiný správce.
* **Stavy aktivity**:
  * _Úspěch_: Určuje, že správce úspěšně resetovat heslo uživatele.
  * _Selhání_: Určuje, že správce se nepodařilo změnit heslo uživatele. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-reset-password-self-service"></a>Typ aktivity: resetování hesla (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel úspěšně resetovat heslo služby [portálu pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com).
* **Aktivita objektu actor**: uživatel, který obnovit své heslo. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který obnovit své heslo. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Určuje, že uživatel úspěšně resetovat vlastní heslo.
  * _Selhání_: označuje, že uživatel nepodařilo resetovat vlastní heslo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvod selhání stavu aktivity**: 
  * _FuzzyPolicyViolationInvalidPassword_: Správce vybrané heslo, které byl automaticky zakázán, protože funkce Microsoft zakázané heslo detekce zjistila, že se moc známé nebo zejména slabé.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Typ aktivity: vlastní sloužit průběh aktivity toku resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: označuje každou konkrétní krok uživatele pokračuje prostřednictvím (například předávání konkrétní heslo resetovat bránu ověřování) jako součást heslo proces obnovení.
* **Aktivita objektu actor**: uživatel, který provedl součástí heslo resetovat toku. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který provedl součástí heslo resetovat toku. Uživatel může být koncový uživatel nebo správce.
* **Stavy aktivity**:
  * _Úspěch_: Určuje, že uživatel úspěšně dokončil konkrétní krok v procesu resetování hesla.
  * _Selhání_: Určuje, že konkrétní krok pro heslo resetovat toku se nezdařilo. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.
* **Důvody stavu aktivity**: najdete v následující tabulce [všech důvodů stav povolenou resetování aktivity](#allowed-values-for-details-column).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Typ aktivity: odemknout účet uživatele (samoobslužné)

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel účtu služby Active Directory byla úspěšně odemknuta bez resetování hesla z [portálu pro resetování hesel Azure AD](https://passwordreset.microsoftonline.com) pomocí funkce služby Active Directory bez resetování odemknutí účtu.
* **Aktivita objektu actor**: uživatel, který odemknuli svůj účet bez resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který odemknuli svůj účet bez resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Povolené aktivity stavy**:
  * _Úspěch_: Určuje, že uživatele byla úspěšně odemknuta svůj vlastní účet.
  * _Selhání_: Určuje, že uživatel se nepodařilo odemknout svůj účet. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Typ aktivity: uživatel zaregistrován pro samoobslužné resetování hesla

Následující seznam popisuje tato aktivita podrobně:

* **Popis aktivity**: Určuje, že uživatel má zaregistrované všechny požadované informace, abyste mohli obnovit své heslo v souladu s zásady resetování hesel aktuálně zadaného klienta. 
* **Aktivita objektu actor**: uživatel, který zaregistrovat pro resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Cíl aktivity**: uživatel, který zaregistrovat pro resetování hesla. Uživatel může být koncový uživatel nebo správce.
* **Povolené aktivity stavy**:
  * _Úspěch_: Určuje, že uživatel úspěšně zaregistrované pro v souladu s aktuální zásady resetování hesel. 
  * _Selhání_: Určuje, že se nepodařilo zaregistrovat pro resetování hesla uživatele. Můžete vybrat řádek pro zobrazení **důvod stavu aktivity** kategorie Další informace o proč došlo k chybě. 

     >[!NOTE]
     >Selhání neznamená, že je uživatel nelze resetovat vlastní heslo. Znamená to, že neskončila proces registrace. Pokud je na svůj účet, který je správný, jako je například telefonní číslo, které není ověřená, i když jejich nebyly ověřeny toto telefonní číslo neověřené data, se můžete pořád použít resetovat heslo. Další informace najdete v tématu [co se stane, když se uživatel zaregistruje?](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers).
     >

## <a name="next-steps"></a>Další postup

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
* [Mám otázku, která není zodpovězená jinde](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Příklad audit aktivity SSPR protokoly ve službě Azure AD"
