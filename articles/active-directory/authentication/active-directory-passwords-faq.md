---
title: Nejčastější dotazy k samoobslužnému resetování hesla – Azure Active Directory
description: Nejčastější dotazy týkající se samoobslužného resetování hesla služby Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: b79eb842105734f0f6c145884d73f886a02dbbbc
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348154"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Nejčastější dotazy k samoobslužnému resetování hesla

Tady jsou uvedené některé nejčastější dotazy, které se týkají všech věcí, které souvisejí s resetováním hesla samoobslužné služby.

Pokud máte obecné otázky týkající se Azure Active Directory (Azure AD) a samoobslužného resetování hesla (SSPR), na které se tady neodkazuje, můžete požádat o pomoc komunitou na [stránce s dotazem Microsoft Q&na Azure Active Directory](/answers/topics/azure-active-directory.html). Mezi členy komunity patří inženýri, správci produktů, odborníky MVP a odborníci na IT.

Tyto nejčastější dotazy jsou rozdělené do následujících částí:

* [Dotazy týkající se registrace resetování hesla](#password-reset-registration)
* [Otázky týkající se resetování hesla](#password-reset)
* [Dotazy týkající se změny hesla](#password-change)
* [Dotazy k sestavám správy hesel](#password-management-reports)
* [Dotazy týkající se zpětného zápisu hesla](#password-writeback)

## <a name="password-reset-registration"></a>Registrace resetování hesla

* **Otázka: můžou moji uživatelé registrovat vlastní data pro resetování hesla?**

  > **Odpověď:** Ano. Pokud je povolené resetování hesla a jsou licencované, můžou uživatelé přejít na registrační portál pro resetování hesla ( https://aka.ms/ssprsetup) k registraci ověřovacích informací). Uživatelé se také mohou registrovat prostřednictvím přístupového panelu ( https://myapps.microsoft.com) . Chcete-li provést registraci prostřednictvím přístupového panelu, je nutné vybrat profilový obrázek, vybrat **profil** a pak vybrat možnost **registrovat pro resetování hesla** .
  >
  > Pokud povolíte [kombinovanou registraci](concept-registration-mfa-sspr-combined.md), můžou se uživatelé zaregistrovat jak pro SSPR, tak i pro Azure AD Multi-Factor Authentication.
* **Otázka: Pokud povolím resetování hesla pro skupinu a potom se rozhodnete ji povolit pro všechny, je nutné znovu zaregistrovat své uživatele?**

  > **A:** Ne. Uživatelům, kteří nastavili data ověřování, není nutné znovu registrovat.
  >
  >
* **Otázka: můžu definovat data resetování hesla jménem uživatelů?**

  > **A:** Ano, můžete to udělat pomocí Azure AD Connect, PowerShellu, [Azure Portal](https://portal.azure.com)nebo [centra pro správu Microsoft 365](https://admin.microsoft.com). Další informace najdete v tématu [data používaná samoobslužným resetováním hesla služby Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: mohu synchronizovat data z bezpečnostních otázek z místního prostředí?**

  > **A:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka: můžou uživatelé zaregistrovat data takovým způsobem, že tato data uvidí jiní uživatelé?**

  > **Odpověď:** Ano. Když uživatelé registrují data pomocí portálu pro registraci resetování hesla, ukládají se data do polí privátního ověřování, která jsou viditelná jenom pro globální správce a uživatele.
  >
  >
* **Otázka: Mám uživatele zaregistrovat, aby mohli použít resetování hesla?**

  > **A:** Ne. Pokud pro své jménem definujete dostatek ověřovacích informací, uživatelé se nebudou muset registrovat. Resetování hesla funguje, pokud máte správně naformátovaná data uložená v příslušných polích v adresáři.
  >
  >
* **Otázka: mohu synchronizovat nebo nastavit pole telefon pro ověření, e-mail ověřování nebo alternativní ověřování jménem uživatelů?**

  > **A:** Pole, která je možné nastavit globálním správcem, jsou definovaná v článku [požadavky na data SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: jak portál pro registraci určuje, jaké možnosti se mají zobrazovat moji uživatelé?**

  > **A:** Portál pro registraci nového hesla zobrazuje jenom možnosti, které jste povolili pro vaše uživatele. Tyto možnosti najdete v části **zásady resetování hesla uživatele** na kartě **Konfigurace** vašeho adresáře. Pokud třeba bezpečnostní otázky nepovolíte, nebudou se uživatelé moct k této možnosti zaregistrovat.
  >
  >
* **Otázka: kdy je uživatel považován za registrovaný?**

  > **A:** Uživatel se považuje za registrovaný pro SSPR, pokud zaregistroval alespoň počet metod, které jsou **potřeba k resetování** hesla, které jste nastavili v [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetování hesla

* **Otázka: zabráníte uživatelům v krátkém časovém limitu pokusů o resetování hesla?**

  > **A:** Ano, k ochraně před zneužitím jsou integrované funkce zabezpečení pro resetování hesla. 
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
* **Otázka: jak dlouho mám čekat na příjem e-mailu, SMS nebo telefonního hovoru z resetování hesla?**

  > **A:** E-maily, zprávy SMS a telefonní hovory by se měly dorazit za minutu. Normální případ je 5 až 20 sekund.
  > Pokud v tomto časovém rámci nedostanete oznámení, postupujte takto:
  > * Ověřte složku s nevyžádanou poštou.
  > * Ověřte, že číslo nebo e-mail, který je kontaktován, je ten, který jste očekávali.
  > * Ověřte, zda jsou ověřovací data v adresáři správně formátována, například + 1 4255551234 nebo *\@ contoso.com uživatele*. 
* **Otázka: Jaké jazyky podporuje resetování hesla?**

  > **A:** Uživatelské rozhraní pro resetování hesla, zprávy SMS a hlasové hovory jsou lokalizovány do stejných jazyků, které jsou podporovány v Microsoft 365.
  >
  >
* **Otázka: jaké části prostředí pro resetování hesla se při nastavování položek značky organizace na kartě Konfigurace v mém adresáři zobrazí logo?**

  > **A:** Portál pro resetování hesla zobrazuje logo vaší organizace a umožňuje nakonfigurovat odkaz "kontaktujte správce", aby odkazoval na vlastní e-mail nebo adresu URL. Všechny e-maily odeslané pomocí resetování hesla zahrnují logo vaší organizace, barvy a jméno v těle e-mailu a přizpůsobují se z nastavení daného konkrétního názvu.
  >
  >
* **Otázka: Jak můžu informovat uživatele o tom, kde přejít k resetování hesel?**

  > **A:** Vyzkoušejte některé návrhy v našem článku o [nasazení SSPR](howto-sspr-deployment.md#plan-communications) .
  >
  >
* **Otázka: mohu tuto stránku použít z mobilního zařízení?**

  > **A:** Ano, Tato stránka funguje na mobilních zařízeních.
  >
  >
* **Otázka: podporujete odemčení místních účtů služby Active Directory, když uživatelé resetují hesla?**

  > **Odpověď:** Ano. Pokud uživatel resetuje heslo, pokud byl zpětný zápis hesla nasazený prostřednictvím Azure AD Connect, účet uživatele se při resetování hesla automaticky odemkne.
  >
  >
* **Otázka: Jak můžu integrovat resetování hesla přímo do uživatelského prostředí pro přihlašování uživatele?**

  > **A:** Pokud jste zákazníkem Azure AD Premium, můžete Microsoft Identity Manager nainstalovat bez dalších poplatků a nasadit místní řešení pro resetování hesla.
  >
  >
* **Otázka: mohu nastavit různé bezpečnostní otázky pro různá národní prostředí?**

  > **A:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka: kolik otázek mohu nakonfigurovat pro možnost ověřování bezpečnostních otázek?**

  > **A:** V [Azure Portal](https://portal.azure.com)můžete nakonfigurovat až 20 vlastních bezpečnostních otázek.
  >
  >
* **Otázka: jak dlouho můžou být bezpečnostní otázky?**

  > **A:** Bezpečnostní otázky můžou mít délku 3 až 200 znaků.
  >
  >
* **Otázka: jak dlouho mohou být odpovědi na bezpečnostní otázky?**

  > **A:** Odpovědi můžou mít délku 3 až 40 znaků.
  >
  >
* **Otázka: jsou zamítnout duplicitní odpovědi na bezpečnostní otázky?**

  > **A:** Ano, zamítneme duplicitní odpovědi na bezpečnostní otázky.
  >
  >
* **Otázka: může uživatel zaregistrovat stejnou bezpečnostní otázku více než jednou?**

  > **A:** Ne. Když uživatel zaregistruje konkrétní otázku, nepůjde na tuto otázku zaregistrovat podruhé.
  >
  >
* **Otázka: je možné nastavit minimální limit bezpečnostních otázek pro registraci a resetování?**

  > **A:** Ano, jeden limit se dá nastavit pro registraci a druhý pro resetování. Pro registraci se může vyžadovat tři až pět bezpečnostních otázek a pro resetování se můžou vyžadovat tři až pět otázek.
  >
  >
* **Otázka: Mám nakonfigurovanou zásadu, která vyžaduje, aby uživatelé používali bezpečnostní otázky pro resetování, ale správcům Azure se zdá, že se nakonfigurují jinak.**

  > **A:** Toto je očekávané chování. Microsoft pro všechny role správců Azure vynucuje přísné výchozí zásady resetování hesel. To brání správcům v používání bezpečnostních otázek. Další informace o této zásadě najdete v tématu [zásady a omezení hesel v Azure Active Directorym](concept-sspr-policy.md) článku.
  >
  >
* **Otázka: Pokud uživatel zaregistroval více než maximální počet otázek požadovaných k resetování, jak se bezpečnostní otázky při resetování vybraly?**

  > **A:** *n* počet otázek zabezpečení vybraných náhodně z celkového počtu otázek, pro které uživatel zaregistroval, kde *N* je množství, které je nastaveno pro **počet otázek vyžadovaných k resetování** možnosti. Pokud například uživatel zaregistroval pět bezpečnostních otázek, ale pro resetování hesla jsou vyžadovány pouze tři, tři z pěti otázek jsou náhodně vybrány a zobrazí se při resetování. Chcete-li zabránit kladivu dotazů, pokud uživatel získá odpovědi na otázky, že se spustí proces výběru.
  >
  >
* **Otázka: jak dlouho jsou platná e-mailová a SMS hesla platná?**

  > **A:** Doba platnosti relace pro resetování hesla je 15 minut. Od spuštění operace resetování hesla má uživatel 15 minut, než se heslo resetuje. Heslo pro jednorázové e-maily a SMS jsou v relaci resetování hesla platné po dobu 5 minut.
  >
  >
* **Otázka: můžu zabránit uživatelům v resetování hesla?**

  > **A:** Ano, pokud použijete skupinu k povolení SSPR, můžete odebrat jednotlivé uživatele ze skupiny, která umožňuje uživatelům resetovat heslo. Pokud je uživatel globálním správcem, uchová si možnost resetovat heslo a tato funkce se nedá zakázat.
  >
  >

## <a name="password-change"></a>Změna hesla

* **Otázka: kde by měl moji uživatelé přejít ke změně hesla?**

  > **A:** Uživatelé můžou změnit svá hesla kdekoli, kde uvidí svůj profil nebo ikonu, jako v pravém horním rohu portálu [Office 365](https://portal.office.com) nebo prostředí [přístupového panelu](https://myapps.microsoft.com) . Uživatelé mohou měnit hesla na [stránce profilu přístupového panelu](https://account.activedirectory.windowsazure.com/r#/profile). Uživatelé můžou být na přihlašovací stránce služby Azure AD automaticky požádáni o změnu hesel, pokud jejich hesla vypršela. Nakonec můžou uživatelé přejít na portál pro [změnu hesel Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) přímo, pokud chtějí měnit hesla.
  >
  >
* **Otázka: je možné, že se uživatelé budou na portálu Office informovat, když vyprší jejich místní heslo?**

  > **A:** Ano, tato možnost je možná dnes, pokud používáte Active Directory Federation Services (AD FS) (AD FS). Pokud používáte AD FS, postupujte podle pokynů v tématu [odesílání deklarací zásad hesel pomocí AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) článku. Pokud používáte synchronizaci hodnot hash hesel, není to ještě dnes. Nesynchronizujeme zásady hesel z místních adresářů, takže nám není možné odesílat oznámení o vypršení platnosti cloudovým prostředím. V obou případech je také možné [upozornit uživatele, jejichž hesla brzy vyprší prostřednictvím prostředí PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Otázka: můžu zablokovat uživatelům změnu hesla?**

  > **A:** Pro cloudové uživatele nejde změny hesel blokovat. U místních uživatelů můžete nastavit možnost **uživatel nemůže změnit možnost hesla** na zaškrtnuto. Vybraní uživatelé nemohou změnit heslo.
  >
  >

## <a name="password-management-reports"></a>Sestavy správy hesel

* **Otázka: jak dlouho trvá zobrazování dat v sestavách správy hesel?**

  > **A:** Data by se měla zobrazit v sestavách správy hesel během 5 až 10 minut. V některých případech může trvat až hodinu, než se zobrazí.
  >
  >
* **Otázka: Jak mohu filtrovat sestavy správy hesel?**

  > **A:** Chcete-li filtrovat sestavy správy hesel, vyberte v horní části sestavy malé lupu na krajní pravé straně popisků sloupců. Pokud chcete dělat bohatší filtrování, můžete si stáhnout sestavu do Excelu a vytvořit kontingenční tabulku.
  >
  >
* **Otázka: Jaký je maximální počet událostí, které jsou uloženy v sestavách správy hesel?**

  > **A:** V sestavách správy hesel se ukládají až 75 000 událostí obnovení hesla nebo resetování hesla, a to až do 30 dnů. Pracujeme na tom, aby se toto číslo rozšířilo, aby se zahrnovaly další události.
  >
  >
* **Otázka: jak daleko zpátky budou sestavy správy hesel přecházet?**

  > **A:** Sestavy správy hesel zobrazují operace, ke kterým došlo během posledních 30 dnů. Pokud teď potřebujete archivovat tato data, můžete sestavy stahovat pravidelně a ukládat je do samostatného umístění.
  >
  >
* **Otázka: existuje maximální počet řádků, které se mohou zobrazit v sestavách správy hesel?**

  > **Odpověď:** Ano. V jedné ze sestav správy hesel se může zobrazit maximálně 75 000 řádků, ať už jsou zobrazené v uživatelském rozhraní nebo se stáhnou.
  >
  >
* **Otázka: je k dispozici rozhraní API pro přístup k datům vytváření sestav pro resetování hesla nebo k registraci?**

  > **Odpověď:** Ano. Informace o tom, jak získat přístup k datům pro vytváření sestav pro resetování hesel, najdete v referenčních informacích k [Azure Log Analytics REST API](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Zpětný zápis hesla

* **Otázka: Jak funguje zpětný zápis hesla na pozadí?**

  > **A:** Informace o tom, co se stane, když povolíte zpětný zápis hesla a jak data přejdou zpět do místního prostředí, najdete v článku [jak zpětný zápis hesla funguje](./tutorial-enable-sspr-writeback.md) .
  >
  >
* **Otázka: jak dlouho trvá zpětný zápis hesla? Existuje zpoždění synchronizace, jako je u synchronizace hodnot hash hesel?**

  > **A:** Zpětný zápis hesla je okamžitý. Jedná se o synchronní kanál, který pracuje se zásadním způsobem, než synchronizace hodnot hash hesel. Zpětný zápis hesla umožňuje uživatelům získat zpětnou vazbu v reálném čase o úspěchu jejich resetování nebo změny hesla. Průměrná doba úspěšného zápisu hesla je pod 500 ms.
  >
  >
* **Otázka: Pokud je můj místní účet zakázaný, jak se to týká můj cloudový účet a přístup?**

  > **A:** Pokud je vaše místní ID zakázané, vaše ID a přístup k vašemu cloudu se také zakáže při dalším intervalu synchronizace prostřednictvím Azure AD Connect. Ve výchozím nastavení je tato synchronizace každých 30 minut.
  >
  >
* **Otázka: Pokud je místní účet omezený místními zásadami hesel služby Active Directory, při změně mého hesla SSPR dodržuje tyto zásady.**

  > **A:** Ano, SSPR spoléhá na a společnost místní zásady hesel služby Active Directory. Tato zásada zahrnuje typické zásady hesel k doméně služby Active Directory a také všechny definované, jemně odstupňované zásady hesel, které jsou zaměřené na uživatele.
  >
  >
* **Otázka: Jaké typy účtů jsou pro použití zpětného zápisu hesla k pro?**

  > **A:** Zpětný zápis hesla funguje u uživatelských účtů, které jsou synchronizované z místní služby Active Directory do služby Azure AD, včetně federovaného, synchronizace hodnot hash hesel a Pass-Through uživatelů ověřování.
  >
  >
* **Otázka: zpětný zápis hesla vynutil zásady hesel moje doména?**

  > **Odpověď:** Ano. Zpětný zápis hesla vynutil stáří hesla, historii, složitost, filtry a další omezení, která můžete zavést na hesla ve vaší místní doméně.
  >
  >
* **Otázka: Jedná se o zabezpečený zpětný zápis hesla?  Jak můžu zajistit napadení?**

  > **A:** Ano, zpětný zápis hesla je zabezpečený. Další informace o několika vrstvách zabezpečení implementovaných službou hesla pro zpětný zápis najdete v části [zabezpečení zpětného zápisu hesla](concept-sspr-writeback.md#password-writeback-security) v článku [Přehled zpětného zápisu](./tutorial-enable-sspr-writeback.md) hesla.
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
* [Co je zpětný zápis hesla a proč byste se o něj měli starat?](./tutorial-enable-sspr-writeback.md)
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco přerušeno. Návody řešit potíže s SSPR?](./troubleshoot-sspr.md)