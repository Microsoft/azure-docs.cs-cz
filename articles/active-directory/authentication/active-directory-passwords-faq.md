---
title: Nejčastější dotazy k samoobslužnému resetování hesla – Azure Active Directory
description: Nejčastější dotazy týkající se samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4001f3c88b676a2786159946a8981823e18ea5f6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274417"
---
# <a name="password-management-frequently-asked-questions"></a>Nejčastější dotazy ke správě hesel

Tady jsou uvedené některé nejčastější dotazy týkající se resetování hesla.

Pokud máte obecnou otázku týkající se Azure Active Directory (Azure AD) a samoobslužného resetování hesla (SSPR), na které tady neodpovíte, můžete požádat o pomoc komunitě [Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Mezi členy komunity patří inženýri, správci produktů, odborníky MVP a odborníci na IT.

Tyto nejčastější dotazy jsou rozdělené do následujících částí:

* [Dotazy týkající se registrace resetování hesla](#password-reset-registration)
* [Otázky týkající se resetování hesla](#password-reset)
* [Dotazy týkající se změny hesla](#password-change)
* [Dotazy k sestavám správy hesel](#password-management-reports)
* [Dotazy týkající se zpětného zápisu hesla](#password-writeback)

## <a name="password-reset-registration"></a>Registrace resetování hesla

* **Otázka:  Můžou moji uživatelé registrovat vlastní data pro resetování hesla?**

  > **Odpověď:** Ano. Pokud je povolené resetování hesla a jsou licencované, můžou uživatelé přejít na registrační portál pro resetování hesla (https://aka.ms/ssprsetup) k registraci ověřovacích informací). Uživatelé se také mohou registrovat prostřednictvím přístupového panelu https://myapps.microsoft.com) (. Chcete-li provést registraci prostřednictvím přístupového panelu, je nutné vybrat profilový obrázek, vybrat **profil**a pak vybrat možnost **registrovat pro resetování hesla** .
  >
  >
* **Otázka:  Pokud povolím resetování hesla pro skupinu a potom se rozhodnete ji povolit pro všechny, všichni uživatelé budou muset registraci znovu zaregistrovat?**

  > **Odpověď:** Ne. Uživatelům, kteří nastavili data ověřování, není nutné znovu registrovat.
  >
  >
* **Otázka:  Můžu v zastoupení uživatelů definovat data resetování hesla?**

  > **Odpověď:** Ano, můžete to udělat pomocí Azure AD Connect, PowerShellu, [Azure Portal](https://portal.azure.com)nebo [centra pro správu Microsoft 365](https://admin.microsoft.com). Další informace najdete v tématu [data používaná samoobslužným resetováním hesla služby Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka:  Můžu synchronizovat data pro bezpečnostní otázky z místního prostředí?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka:  Můžou uživatelé zaregistrovat data takovým způsobem, že tato data uvidí jiní uživatelé?**

  > **Odpověď:** Ano. Když uživatelé registrují data pomocí portálu pro registraci resetování hesla, ukládají se data do polí privátního ověřování, která jsou viditelná jenom pro globální správce a uživatele.
  >
  >
* **Otázka:  Musí se uživatelé zaregistrovat, aby mohli použít resetování hesla?**

  > **Odpověď:** Ne. Pokud pro své jménem definujete dostatek ověřovacích informací, uživatelé se nebudou muset registrovat. Resetování hesla funguje, pokud máte správně naformátovaná data uložená v příslušných polích v adresáři.
  >
  >
* **Otázka:  Můžu jménem uživatelů synchronizovat nebo nastavit telefonní pole telefonu pro ověřování, e-mail ověřování nebo alternativní ověřování.**

  > **Odpověď:** Pole, která je možné nastavit globálním správcem, jsou definovaná v článku [požadavky na data SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka:  Jak portál pro registraci určuje, které možnosti chcete uživatelům zobrazit?**

  > **Odpověď:** Portál pro registraci nového hesla zobrazuje jenom možnosti, které jste povolili pro vaše uživatele. Tyto možnosti najdete v části **zásady resetování hesla uživatele** na kartě **Konfigurace** vašeho adresáře. Pokud třeba bezpečnostní otázky nepovolíte, nebudou se uživatelé moct k této možnosti zaregistrovat.
  >
  >
* **Otázka:  Kdy je uživatel považován za registrovaný?**

  > **Odpověď:** Uživatel se považuje za registrovaný pro SSPR, pokud zaregistroval alespoň počet metod, které jsou **potřeba k resetování** hesla, které jste nastavili v [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetování hesla

* **Otázka:  Zabráníte uživatelům v krátké době více pokusů o resetování hesla?**

  > **Odpověď:** Ano, k ochraně před zneužitím jsou integrované funkce zabezpečení pro resetování hesla. 
  >
  > Uživatelé můžou v průběhu 24 hodin vyzkoušet jenom pět pokusů o resetování hesla, než se odblokuje po dobu 24 hodin. 
  >
  > Uživatelé se můžou pokusit ověřit telefonní číslo, odeslat SMS nebo ověřit bezpečnostní otázky a odpovědi jenom pětkrát za hodinu, než se zablokují po dobu 24 hodin. 
  >
  > Uživatelé můžou poslat e-mail maximálně desetkrát během 10 minut, než se odblokuje po dobu 24 hodin.
  >
  > Čítače se resetují, jakmile uživatel resetuje heslo.
  >
  >
* **Otázka:  Jak dlouho mám čekat na příjem e-mailu, SMS nebo telefonního hovoru z resetování hesla?**

  > **Odpověď:** E-maily, zprávy SMS a telefonní hovory by se měly dorazit za minutu. Normální případ je 5 až 20 sekund.
  > Pokud v tomto časovém rámci nedostanete oznámení, postupujte takto:
  > * Ověřte složku s nevyžádanou poštou.
  > * Ověřte, že číslo nebo e-mail, který je kontaktován, je ten, který jste očekávali.
  > * Ověřte, zda jsou ověřovací data v adresáři správně formátována, například + 1 4255551234 nebo *contoso.com uživatele\@* . 
* **Otázka:  Jaké jazyky podporuje resetování hesla?**

  > **Odpověď:** Uživatelské rozhraní pro resetování hesla, zprávy SMS a hlasové hovory jsou lokalizovány do stejných jazyků, které jsou podporovány v systému Office 365.
  >
  >
* **Otázka:  Jaké části prostředí pro resetování hesla se zobrazí při nastavování položek branding organizace na kartě Konfigurace v adresáři?**

  > **Odpověď:** Portál pro resetování hesla zobrazuje logo vaší organizace a umožňuje nakonfigurovat odkaz "kontaktujte správce", aby odkazoval na vlastní e-mail nebo adresu URL. Všechny e-maily odeslané pomocí resetování hesla zahrnují logo vaší organizace, barvy a jméno v těle e-mailu a přizpůsobují se z nastavení daného konkrétního názvu.
  >
  >
* **Otázka:  Jak můžu informovat uživatele o tom, kde přejít k resetování hesel?**

  > **Odpověď:** Vyzkoušejte některé návrhy v našem článku o [nasazení SSPR](howto-sspr-deployment.md#communications-plan) .
  >
  >
* **Otázka:  Můžu tuto stránku použít z mobilního zařízení?**

  > **Odpověď:** Ano, Tato stránka funguje na mobilních zařízeních.
  >
  >
* **Otázka:  Podporujete odemknutí místních účtů služby Active Directory, když uživatelé resetují hesla?**

  > **Odpověď:** Ano. Pokud uživatel resetuje heslo, pokud byl zpětný zápis hesla nasazený prostřednictvím Azure AD Connect, účet uživatele se při resetování hesla automaticky odemkne.
  >
  >
* **Otázka:  Jak můžu integrovat resetování hesla přímo do uživatelského prostředí pro přihlašování z desktopu?**

  > **Odpověď:** Pokud jste zákazníkem Azure AD Premium, můžete Microsoft Identity Manager nainstalovat bez dalších poplatků a nasadit místní řešení pro resetování hesla.
  >
  >
* **Otázka:  Můžu pro různá národní prostředí nastavit různé bezpečnostní otázky?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka:  Kolik otázek můžu nakonfigurovat pro možnost ověřování bezpečnostních otázek?**

  > **Odpověď:** V [Azure Portal](https://portal.azure.com)můžete nakonfigurovat až 20 vlastních bezpečnostních otázek.
  >
  >
* **Otázka:  Jak dlouho můžou být bezpečnostní otázky?**

  > **Odpověď:** Bezpečnostní otázky můžou mít délku 3 až 200 znaků.
  >
  >
* **Otázka:  Jak dlouho můžou odpovědi na bezpečnostní otázky?**

  > **Odpověď:** Odpovědi můžou mít délku 3 až 40 znaků.
  >
  >
* **Otázka:  Jsou duplicitní odpovědi na bezpečnostní otázky odmítnuty?**

  > **Odpověď:** Ano, zamítneme duplicitní odpovědi na bezpečnostní otázky.
  >
  >
* **Otázka:  Může uživatel zaregistrovat stejnou bezpečnostní otázku více než jednou?**

  > **Odpověď:** Ne. Když uživatel zaregistruje konkrétní otázku, nepůjde na tuto otázku zaregistrovat podruhé.
  >
  >
* **Otázka:  Je možné nastavit minimální limit bezpečnostních otázek pro registraci a resetování?**

  > **Odpověď:** Ano, jeden limit se dá nastavit pro registraci a druhý pro resetování. Pro registraci se může vyžadovat tři až pět bezpečnostních otázek a pro resetování se můžou vyžadovat tři až pět otázek.
  >
  >
* **Otázka:  Mám nakonfigurovanou zásadu, která vyžaduje, aby uživatelé používali bezpečnostní otázky pro resetování, ale správcům Azure se zdá, že se nakonfigurují jinak.**

  > **Odpověď:** Toto je očekávané chování. Microsoft pro všechny role správců Azure vynucuje přísné výchozí zásady resetování hesel. To brání správcům v používání bezpečnostních otázek. Další informace o této zásadě najdete v tématu [zásady a omezení hesel v Azure Active Directorym](concept-sspr-policy.md) článku.
  >
  >
* **Otázka:  Pokud uživatel zaregistroval více než maximální počet otázek požadovaných k resetování, jak se bezpečnostní otázky při resetování vybraly?**

  > **Odpověď:** *N* je vybráno náhodné množství bezpečnostních otázek z celkového počtu otázek, pro které uživatel zaregistroval, kde *N* je hodnota, která je nastavena pro **počet otázek vyžadovaných k resetování** možnosti. Pokud například uživatel zaregistroval pět bezpečnostních otázek, ale pro resetování hesla jsou vyžadovány pouze tři, tři z pěti otázek jsou náhodně vybrány a zobrazí se při resetování. Chcete-li zabránit kladivu dotazů, pokud uživatel získá odpovědi na otázky, že se spustí proces výběru.
  >
  >
* **Otázka:  Jak dlouho jsou e-maily a jednorázová hesla SMS platná?**

  > **Odpověď:** Doba platnosti relace pro resetování hesla je 15 minut. Od spuštění operace resetování hesla má uživatel 15 minut, než se heslo resetuje. Heslo pro jednorázové e-maily a SMS jsou v relaci resetování hesla platné po dobu 5 minut.
  >
  >
* **Otázka:  Můžu uživatelům zabránit v resetování hesla?**

  > **Odpověď:** Ano, pokud použijete skupinu k povolení SSPR, můžete odebrat jednotlivé uživatele ze skupiny, která umožňuje uživatelům resetovat heslo. Pokud je uživatel globálním správcem, uchová si možnost resetovat heslo a tato funkce se nedá zakázat.
  >
  >

## <a name="password-change"></a>Změna hesla

* **Otázka:  Kam by měl moji uživatelé přejít ke změně hesla?**

  > **Odpověď:** Uživatelé můžou změnit svá hesla kdekoli, kde uvidí svůj profil nebo ikonu, jako v pravém horním rohu portálu [Office 365](https://portal.office.com) nebo prostředí [přístupového panelu](https://myapps.microsoft.com) . Uživatelé mohou měnit hesla na [stránce profilu přístupového panelu](https://account.activedirectory.windowsazure.com/r#/profile). Uživatelé můžou být na přihlašovací stránce služby Azure AD automaticky požádáni o změnu hesel, pokud jejich hesla vypršela. Nakonec můžou uživatelé přejít na portál pro [změnu hesel Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) přímo, pokud chtějí měnit hesla.
  >
  >
* **Otázka:  Můžou se uživatelé na portálu Office informovat, když vyprší jejich místní heslo?**

  > **Odpověď:** Ano, tato možnost je možná dnes, pokud používáte Active Directory Federation Services (AD FS) (AD FS). Pokud používáte AD FS, postupujte podle pokynů v tématu [odesílání deklarací zásad hesel pomocí AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) článku. Pokud používáte synchronizaci hodnot hash hesel, není to ještě dnes. Nesynchronizujeme zásady hesel z místních adresářů, takže nám není možné odesílat oznámení o vypršení platnosti cloudovým prostředím. V obou případech je také možné [upozornit uživatele, jejichž hesla brzy vyprší prostřednictvím prostředí PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Otázka:  Můžu uživatelům zablokovat změnu hesla?**

  > **Odpověď:** Pro cloudové uživatele nejde změny hesel blokovat. U místních uživatelů můžete nastavit možnost **uživatel nemůže změnit možnost hesla** na zaškrtnuto. Vybraní uživatelé nemohou změnit heslo.
  >
  >

## <a name="password-management-reports"></a>Sestavy správy hesel

* **Otázka:  Jak dlouho trvá, než se data zobrazí v sestavách správy hesel?**

  > **Odpověď:** Data by se měla zobrazit v sestavách správy hesel během 5 až 10 minut. V některých případech může trvat až hodinu, než se zobrazí.
  >
  >
* **Otázka:  Jak mohu filtrovat sestavy správy hesel?**

  > **Odpověď:** Chcete-li filtrovat sestavy správy hesel, vyberte v horní části sestavy malé lupu na krajní pravé straně popisků sloupců. Pokud chcete dělat bohatší filtrování, můžete si stáhnout sestavu do Excelu a vytvořit kontingenční tabulku.
  >
  >
* **Otázka: Jaký je maximální počet událostí, které jsou uloženy v sestavách správy hesel?**

  > **Odpověď:** V sestavách správy hesel se ukládají až 75 000 událostí obnovení hesla nebo resetování hesla, a to až do 30 dnů. Pracujeme na tom, aby se toto číslo rozšířilo, aby se zahrnovaly další události.
  >
  >
* **Otázka:  Jak daleko zpátky budou sestavy správy hesel přecházet?**

  > **Odpověď:** Sestavy správy hesel zobrazují operace, ke kterým došlo během posledních 30 dnů. Pokud teď potřebujete archivovat tato data, můžete sestavy stahovat pravidelně a ukládat je do samostatného umístění.
  >
  >
* **Otázka:  Je v sestavách správy hesel k dispozici maximální počet řádků, který se může zobrazit?**

  > **Odpověď:** Ano. V jedné ze sestav správy hesel se může zobrazit maximálně 75 000 řádků, ať už jsou zobrazené v uživatelském rozhraní nebo se stáhnou.
  >
  >
* **Otázka:  Existuje rozhraní API pro přístup k datům pro resetování hesla nebo k registračním údajům.**

  > **Odpověď:** Ano. Informace o tom, jak získat přístup ke streamu pro vytváření sestav pro resetování hesel, najdete v tématu [informace o tom, jak programově přistupovat k událostem vytváření sestav](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)
  >
  >

## <a name="password-writeback"></a>Zpětný zápis hesel

* **Otázka:  Jak funguje zpětný zápis hesla na pozadí?**

  > **Odpověď:** Informace o tom, co se stane, když povolíte zpětný zápis hesla a jak data přejdou zpět do místního prostředí, najdete v článku [jak zpětný zápis hesla funguje](howto-sspr-writeback.md) .
  >
  >
* **Otázka:  Jak dlouho bude zpětný zápis hesla trvat? Existuje zpoždění synchronizace, jako je u synchronizace hodnot hash hesel?**

  > **Odpověď:** Zpětný zápis hesla je okamžitý. Jedná se o synchronní kanál, který pracuje se zásadním způsobem, než synchronizace hodnot hash hesel. Zpětný zápis hesla umožňuje uživatelům získat zpětnou vazbu v reálném čase o úspěchu jejich resetování nebo změny hesla. Průměrná doba úspěšného zápisu hesla je pod 500 ms.
  >
  >
* **Otázka:  Pokud je můj místní účet zakázaný, jak se to týká můj cloudový účet a přístup?**

  > **Odpověď:** Pokud je vaše místní ID zakázané, vaše ID a přístup k vašemu cloudu se také zakáže při dalším intervalu synchronizace prostřednictvím Azure AD Connect. Ve výchozím nastavení je tato synchronizace každých 30 minut.
  >
  >
* **Otázka:  Pokud je místní účet omezený místními zásadami hesel služby Active Directory, při změně mého hesla SSPR dodržuje tyto zásady?**

  > **Odpověď:** Ano, SSPR spoléhá na a společnost místní zásady hesel služby Active Directory. Tato zásada zahrnuje typické zásady hesel k doméně služby Active Directory a také všechny definované, jemně odstupňované zásady hesel, které jsou zaměřené na uživatele.
  >
  >
* **Otázka:  V jakých typech účtů funguje zpětný zápis hesla?**

  > **Odpověď:** Zpětný zápis hesla funguje u uživatelských účtů, které jsou synchronizované z místní služby Active Directory do služby Azure AD, včetně federovaného, synchronizace hodnot hash hesel a předávacích uživatelů Autentication.
  >
  >
* **Otázka:  Vynutila zpětný zápis hesla moje moje zásady hesel moje doména?**

  > **Odpověď:** Ano. Zpětný zápis hesla vynutil stáří hesla, historii, složitost, filtry a další omezení, která můžete zavést na hesla ve vaší místní doméně.
  >
  >
* **Otázka:  Je zpětný zápis hesla zabezpečený?  Jak můžu zajistit napadení?**

  > **Odpověď:** Ano, zpětný zápis hesla je zabezpečený. Další informace o několika vrstvách zabezpečení implementovaných službou hesla pro zpětný zápis najdete v části [zabezpečení zpětného zápisu hesla](concept-sspr-writeback.md#password-writeback-security) v článku [Přehled zpětného zápisu](howto-sspr-writeback.md) hesla.
  >
  >

## <a name="next-steps"></a>Další kroky

* [Jak dokončit úspěšné zavedení SSPR?](howto-sspr-deployment.md)
* [Resetování nebo změna hesla](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrace samoobslužného resetování hesla](../user-help/active-directory-passwords-reset-register.md)
* [Máte dotaz k licencování?](concept-sspr-licensing.md)
* [Jaká data používá SSPR a která data byste měli naplnit pro vaše uživatele?](howto-sspr-authenticationdata.md)
* [Které metody ověřování jsou dostupné pro uživatele?](concept-sspr-howitworks.md#authentication-methods)
* [Jaké jsou možnosti zásad se SSPR?](concept-sspr-policy.md)
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](howto-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
