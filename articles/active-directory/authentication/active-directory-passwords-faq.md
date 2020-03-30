---
title: Nejčastější dotazy k samoobslužné resetování hesla – Azure Active Directory
description: Nejčastější dotazy týkající se samoobslužného resetování hesla Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e1cc9dde6bb2b6ae47affaed4c557f3746a681b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061450"
---
# <a name="password-management-frequently-asked-questions"></a>Nejčastější dotazy týkající se správy hesel

Níže jsou uvedeny některé často kladené otázky (FAQ) pro všechny věci týkající se resetování hesla.

Pokud máte obecnou otázku týkající se Azure Active Directory (Azure AD) a samoobslužné resetování hesla (SSPR), na které tady není odpověď, můžete požádat komunitu o pomoc na [fóru Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členy komunity jsou inženýři, produktoví manažeři, MVP a další IT profesionálové.

Tyto nejčastější dotazy jsou rozděleny do následujících částí:

* [Otázky týkající se registrace resetování hesla](#password-reset-registration)
* [Otázky týkající se resetování hesla](#password-reset)
* [Otázky týkající se změny hesla](#password-change)
* [Otázky týkající se sestav správy hesel](#password-management-reports)
* [Otázky týkající se zpětného zápisu hesla](#password-writeback)

## <a name="password-reset-registration"></a>Registrace resetování hesla

* **Otázka: Mohou moji uživatelé zaregistrovat vlastní data pro resetování hesla?**

  > **Odpověď:** Ano. Dokud je povoleno resetování hesla a jsou licencovány, mohou uživatelé přejít na registrační portál pro obnovení hesla (https://aka.ms/ssprsetup) zaregistrovat své ověřovací informace. Uživatelé se mohou takéhttps://myapps.microsoft.com)zaregistrovat prostřednictvím přístupového panelu ( . Chcete-li se zaregistrovat prostřednictvím přístupového panelu, musí vybrat svůj profilový obrázek, vybrat **profil**a pak vybrat možnost **Registrovat pro obnovení hesla.**
  >
  >
* **Otázka: Pokud povolím resetování hesla pro skupinu a pak se rozhodnu ji povolit pro všechny, jsou moji uživatelé povinni znovu zaregistrovat?**

  > **Odpověď:** Ne. Uživatelé, kteří mají vyplněná ověřovací data, se nemusí znovu registrovat.
  >
  >
* **Otázka: Lze definovat data pro resetování hesla jménem svých uživatelů?**

  > **A:** Ano, můžete tak učinit pomocí Azure AD Connect, PowerShellu, [portálu Azure](https://portal.azure.com)nebo [Centra pro správu Microsoftu 365](https://admin.microsoft.com). Další informace najdete [v tématu Data používaná samoobslužným heslem Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: Můžu synchronizovat data pro otázky zabezpečení z místního prostředí?**

  > **A:** Ne, dnes to není možné.
  >
  >
* **Otázka: Mohou moji uživatelé data registrovat tak, aby ostatní uživatelé tato data neviděli?**

  > **Odpověď:** Ano. Když uživatelé registrují data pomocí registračního portálu pro obnovení hesla, jsou data uložena do soukromých ověřovacích polí, která jsou viditelná pouze globálním správcům a uživateli.
  >
  >
* **Otázka: Musí být moji uživatelé před použitím resetování hesla zaregistrováni?**

  > **Odpověď:** Ne. Pokud definujete dostatek ověřovacích informací jejich jménem, uživatelé se nemusí registrovat. Resetování hesla funguje tak dlouho, dokud jste správně naformátovali data uložená v příslušných polích v adresáři.
  >
  >
* **Otázka: Můžu jménem uživatelů synchronizovat nebo nastavit ověřovací telefon, ověřovací e-mail nebo alternativní ověřovací telefonní pole?**

  > **A:** Pole, která lze nastavit globálním správcem, jsou definována v článku [Požadavky na data sspr](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: Jak registrační portál určuje, které možnosti zobrazit svým uživatelům?**

  > **A:** Registrační portál pro obnovení hesla zobrazuje pouze možnosti, které jste povolili pro uživatele. Tyto možnosti najdete v části **Zásady pro obnovení uživatelského hesla** na kartě **Konfigurace** adresáře. Pokud například nepovolíte bezpečnostní otázky, uživatelé se nebudou moci pro tuto možnost zaregistrovat.
  >
  >
* **Otázka: Kdy je uživatel považován za registrovaného?**

  > **A:** Uživatel se považuje za registrovaný pro sspr, pokud zaregistrovali alespoň **počet metod potřebných k resetování** hesla, které jste nastavili na [webu Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetování hesla

* **Otázka: Zabráníte uživatelům v několika pokusech o resetování hesla v krátkém časovém období?**

  > **A:** Ano, existují bezpečnostní funkce integrované do resetování hesla, které chrání před zneužitím. 
  >
  > Uživatelé mohou během 24 hodin vyzkoušet pouze pět pokusů o resetování hesla, než budou na 24 hodin uzamčeni. 
  >
  > Uživatelé se mohou pokusit ověřit telefonní číslo, odeslat SMS nebo ověřit bezpečnostní otázky a odpovědi pouze pětkrát během jedné hodiny, než budou uzamčeni po dobu 24 hodin. 
  >
  > Uživatelé mohou odeslat e-mail maximálně 10krát během 10 minut, než budou uzamčeni po dobu 24 hodin.
  >
  > Čítače jsou resetovány, jakmile uživatel resetuje své heslo.
  >
  >
* **Otázka: Jak dlouho mám čekat na přijetí e-mailu, SMS nebo telefonního hovoru z resetování hesla?**

  > **A:** E-maily, SMS zprávy a telefonní hovory by měly být doručeny za méně než minutu. Normální případ je 5 až 20 sekund.
  > Pokud v tomto časovém rámci neobdržíte oznámení:
  > * Zkontrolujte složku nevyžádané pošty.
  > * Zkontrolujte, zda je číslo nebo e-mail, který jste kontaktovali, ten, který očekáváte.
  > * Zkontrolujte, zda jsou ověřovací data v adresáři správně formátována, například +1 4255551234 nebo *uživatelská\@contoso.com*. 
* **Otázka: Jaké jazyky jsou podporovány resetováním hesla?**

  > **A:** UI pro resetování hesla, SMS zprávy a hlasová volání jsou lokalizovány ve stejných jazycích, které jsou podporované v Office 365.
  >
  >
* **Otázka: Jaké části prostředí pro resetování hesla se označují, když nakonfigurované kartě adresáře nastavím položky značky organizace?**

  > **A:** Portál pro obnovení hesla zobrazuje logo vaší organizace a umožňuje nakonfigurovat odkaz Kontaktovat správce tak, aby ukazoval na vlastní e-mail nebo adresu URL. Všechny e-maily odeslané resetováním hesla obsahují logo, barvy a název vaší organizace v textu e-mailu a jsou přizpůsobeny v nastavení daného jména.
  >
  >
* **Otázka: Jak mohu uživatele poučit o tom, kam mají resetovat hesla?**

  > **A:** Vyzkoušejte některé návrhy v našem článku [o nasazení sspr.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **Otázka: Mohu tuto stránku používat z mobilního zařízení?**

  > **A:** Ano, tato stránka funguje na mobilních zařízeních.
  >
  >
* **Otázka: Podporujete odemknutí místních účtů služby Active Directory při resetování hesel uživateli?**

  > **Odpověď:** Ano. Když uživatel resetuje své heslo, pokud bylo pomocí služby Azure AD Connect nasazeno zpětné heslo, účet tohoto uživatele se automaticky odemkne při resetování hesla.
  >
  >
* **Otázka: Jak lze integrovat resetování hesla přímo do prostředí přihlašování na ploše uživatele?**

  > **A:** Pokud jste zákazníkazure AD Premium, můžete nainstalovat Microsoft Identity Manager bez dalších nákladů a nasadit místní řešení pro resetování hesla.
  >
  >
* **Otázka: Lze nastavit různé bezpečnostní otázky pro různá národní prostředí?**

  > **A:** Ne, dnes to není možné.
  >
  >
* **Otázka: Kolik otázek lze nakonfigurovat pro možnost ověřování bezpečnostních otázek?**

  > **A:** Na [webu Azure Portal](https://portal.azure.com)můžete nakonfigurovat až 20 vlastních otázek týkajících se zabezpečení .
  >
  >
* **Otázka: Jak dlouho mohou být bezpečnostní otázky?**

  > **A:** Bezpečnostní otázky mohou mít dlouhou 3 až 200 znaků.
  >
  >
* **Otázka: Jak dlouho mohou být odpovědi na bezpečnostní otázky?**

  > **A:** Odpovědi mohou mít dlouhou 3 až 40 znaků.
  >
  >
* **Otázka: Jsou duplicitní odpovědi na bezpečnostní otázky odmítnuty?**

  > **A:** Ano, odmítáme duplicitní odpovědi na bezpečnostní otázky.
  >
  >
* **Otázka: Může uživatel zaregistrovat stejnou bezpečnostní otázku více než jednou?**

  > **Odpověď:** Ne. Poté, co uživatel zaregistruje konkrétní otázku, nemůže se zaregistrovat pro tuto otázku podruhé.
  >
  >
* **Otázka: Je možné nastavit minimální limit bezpečnostních otázek pro registraci a reset?**

  > **A:** Ano, jeden limit lze nastavit pro registraci a druhý pro reset. Pro registraci mohou být vyžadovány tři až pět bezpečnostních otázek a pro reset ování mohou být vyžadovány tři až pět otázek.
  >
  >
* **Otázka: Nakonfiguroval jsem zásady tak, aby vyžadovaly, aby uživatelé používali otázky zabezpečení k resetování, ale správci Azure se zdají být nakonfigurovaní jinak.**

  > **A:** Toto je očekávané chování. Microsoft pro všechny role správců Azure vynucuje přísné výchozí zásady resetování hesel. To zabrání správcům v použití bezpečnostních otázek. Další informace o této zásadách najdete v článku [Zásady a omezení hesla.](concept-sspr-policy.md)
  >
  >
* **Otázka: Pokud uživatel zaregistroval více než maximální počet otázek potřebných k resetování, jak jsou bezpečnostní otázky vybrány během resetování?**

  > **A:** *N* počet bezpečnostních otázek jsou vybrány náhodně z celkového počtu otázek, které uživatel zaregistroval, kde *N* je částka, která je nastavena pro počet **otázek potřebných k resetování** možnost. Pokud například uživatel zaregistroval pět bezpečnostních otázek, ale k resetování hesla jsou vyžadovány pouze tři, jsou náhodně vybrány tři z pěti otázek, které jsou uvedeny při resetování. Chcete-li zabránit zatloukání otázek, pokud uživatel získá odpovědi na otázky nesprávné proces výběru začíná znovu.
  >
  >
* **Otázka: Jak dlouho jsou e-mailové a SMS jednorázové přístupové kódy platné?**

  > **A:** Životnost relace pro resetování hesla je 15 minut. Od začátku operace resetování hesla má uživatel 15 minut na resetování hesla. E-mail a SMS jednorázový přístupový kód jsou platné po dobu 5 minut během relace resetování hesla.
  >
  >
* **Otázka: Mohu uživatelům zablokovat resetování hesla?**

  > **A:** Ano, pokud používáte skupinu k povolení sspr, můžete odebrat jednotlivé uživatele ze skupiny, která umožňuje uživatelům obnovit své heslo. Pokud je uživatel globálním správcem, zachová si možnost resetovat své heslo a nelze jej zakázat.
  >
  >

## <a name="password-change"></a>Změna hesla

* **Otázka: Kde by měli moji uživatelé změnit svá hesla?**

  > **A:** Uživatelé si můžou změnit hesla kdekoli, kde uvidí svůj profilový obrázek nebo ikonu, třeba v pravém horním rohu portálu [Office 365](https://portal.office.com) nebo [přístupového panelu.](https://myapps.microsoft.com) Uživatelé mohou změnit svá hesla na [stránce Profil přístupového panelu](https://account.activedirectory.windowsazure.com/r#/profile). Uživatelé mohou být také požádáni o automatické změně hesla na přihlašovací stránce Azure AD, pokud jejich hesla vypršela. Nakonec uživatelé mohou přejít na [portál změny hesla Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) přímo, pokud chtějí změnit svá hesla.
  >
  >
* **Otázka: Můžou být moji uživatelé upozorněni na portálu Office, když vyprší platnost jejich místního hesla?**

  > **A:** Ano, to je možné dnes, pokud používáte službu AD FS (Active Directory Federation Services). Pokud používáte ad fs, postupujte podle pokynů v [odesílání deklarací zásad hesel s ad FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) článku. Pokud používáte synchronizaci hodnot hash hesel, není to dnes možné. Zásady hesel nesynchronizujeme z místních adresářů, takže není možné, abychom do cloudových prostředí zveřejňovali oznámení o vypršení platnosti. V obou případech je také možné [upozornit uživatele, jejichž hesla brzy vyprší prostřednictvím prostředí PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Otázka: Mohu uživatelům zabránit ve změně hesla?**

  > **A:** Pro uživatele pouze pro cloud nelze blokovat změny hesel. Pro místní uživatele můžete nastavit možnost **uživatele,** aby nemohl změnit možnost hesla na vybranou. Vybraní uživatelé nemohou změnit své heslo.
  >
  >

## <a name="password-management-reports"></a>Sestavy správy hesel

* **Otázka: Jak dlouho trvá, než se data zobrazí v sestavách správy hesel?**

  > **A:** Data by se měla objevit v sestavách správy hesel za 5 až 10 minut. V některých případech může trvat až hodinu, než se zobrazí.
  >
  >
* **Otázka: Jak mohu filtrovat sestavy správy hesel?**

  > **A:** Chcete-li filtrovat sestavy správy hesel, vyberte malé lupy vpravo od popisků sloupců v horní části sestavy. Pokud chcete provést bohatší filtrování, můžete si sestavu stáhnout do aplikace Excel a vytvořit kontingenční tabulku.
  >
  >
* **Otázka: Jaký je maximální počet událostí, které jsou uloženy v sestavách správy hesel?**

  > **A:** Až 75 000 událostí registrace obnovení hesla nebo obnovení hesla je uloženo v sestavách správy hesel, které se nacházejí až do 30 dnů. Pracujeme na rozšíření tohoto čísla, aby zahrnovala více událostí.
  >
  >
* **Otázka: Jak daleko zpět jsou sestavy správy hesel?**

  > **A:** Sestavy správy hesel zobrazují operace, ke kterým došlo během posledních 30 dnů. Prozatím, pokud potřebujete tato data archivovat, můžete sestavy pravidelně stahovat a ukládat je na samostatném místě.
  >
  >
* **Otázka: Existuje maximální počet řádků, které se mohou zobrazit v sestavách správy hesel?**

  > **Odpověď:** Ano. Maximálně 75 000 řádků se může zobrazit v některé z sestav správy hesel, ať už jsou zobrazeny v unovém rozhraní nebo jsou staženy.
  >
  >
* **Otázka: Existuje rozhraní API pro přístup k datům pro resetování hesla nebo registraci?**

  > **Odpověď:** Ano. Informace o tom, jak získat přístup k datovému proudu pro obnovení hesla, najdete v [tématu Informace o tom, jak programově přistupovat k událostem vykazování resetování hesla](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zpětný zápis hesla

* **Otázka: Jak funguje zpětný zápis hesla na pozadí?**

  > **A:** Vysvětlení, co se stane, když povolíte zpětný zápis hesla a jak data procházejí systémem zpět do místního prostředí, najdete v článku [Jak funguje zpětný zápis hesla.](howto-sspr-writeback.md)
  >
  >
* **Otázka: Jak dlouho trvá zápis hesla do práce? Existuje zpoždění synchronizace, jako je tomu u synchronizace hash hesla?**

  > **A:** Zpětný zápis hesla je okamžitý. Jedná se o synchronní kanál, který funguje zásadně odlišně než synchronizace hodnot hash hesel. Zpětný zápis hesla umožňuje uživatelům získat zpětnou vazbu v reálném čase o úspěchu jejich resetování nebo změny operace. Průměrná doba úspěšného zpětného zápisu hesla je méně než 500 ms.
  >
  >
* **Otázka: Pokud je můj místní účet deaktivován, jak se můj cloudový účet a přístup týká?**

  > **A:** Pokud je vaše místní ID zakázané, vaše Cloud ID a přístup se také deaktivují v dalším intervalu synchronizace prostřednictvím služby Azure AD Connect. Ve výchozím nastavení je tato synchronizace každých 30 minut.
  >
  >
* **Otázka: Pokud je můj místní účet omezen místními zásadami hesel služby Active Directory, dodržuje při změně hesla tuto zásadu obnovení služby Pro bohabojný systém?**

  > **A:** Ano, samoobslužné resetované služby zabezpečení závisí na místních zásadách pro hesla služby Active Directory a řídí je. Tato zásada zahrnuje typické zásady hesel domény služby Active Directory a všechny definované, jemně odstupňované zásady hesel, které jsou zaměřeny na uživatele.
  >
  >
* **Otázka: Pro jaké typy účtů funguje zpětný zápis hesla?**

  > **A:** Zpětný zápis hesla funguje pro uživatelské účty, které jsou synchronizovány z místní služby Active Directory do služby Azure AD, včetně federované, synchronizované hodnoty hash hesla a uživatelů předávací autentication.
  >
  >
* **Otázka: Vynucuje zpětný zápis hesla zásady hesel mé domény?**

  > **Odpověď:** Ano. Zpětný zápis hesla vynucuje stáří hesla, historii, složitost, filtry a další omezení, která můžete u hesel v místní doméně zavést.
  >
  >
* **Otázka: Je zpětný zápis hesla bezpečný?  Jak si můžu být jistý, že mě nehacknou?**

  > **A:** Ano, zpětný zápis hesla je bezpečný. Další informace o více vrstvách zabezpečení implementovaných službou zpětného zápisu hesla naleznete v části [Zabezpečení zpětného zápisu hesla](concept-sspr-writeback.md#password-writeback-security) v článku [Přehled zpětného zápisu hesla.](howto-sspr-writeback.md)
  >
  >

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace pro samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a jaká data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco rozbité. Jak lze vyřešit probléms sspr?](active-directory-passwords-troubleshoot.md)
