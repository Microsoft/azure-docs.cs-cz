---
title: Samoobslužného resetování hesel – nejčastější dotazy – Azure Active Directory
description: Nejčastější dotazy o hesla pomocí samoobslužné služby Azure AD resetovat
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 7f54877f9218cbb0c2ac8cca86c580b7f1aa6c83
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33869746"
---
# <a name="password-management-frequently-asked-questions"></a>Nejčastější dotazy se správou hesel

Níže jsou uvedeny některé nejčastější dotazy (FAQ) pro všechny věci týkající se vytvoření nového hesla.

Pokud máte obecný dotaz týkající se služby Azure Active Directory (Azure AD) a samoobslužné resetování hesla (SSPR), není zde zodpovězen, můžete požádat komunitou o pomoc na [fórum Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Členové komunity služby zahrnují engineers, správce produktu, MVP a ostatními Odborníci v oblasti IT.

Tyto nejčastější dotazy je rozdělená do následujících částí:

* [Registrace pro resetování dotazy týkající se heslo](#password-reset-registration)
* [Dotazy o resetování hesla](#password-reset)
* [Dotazy týkající se změna hesla](#password-change)
* [Dotazy týkající se sestav správy hesel](#password-management-reports)
* [Dotazy týkající se zpětný zápis hesla](#password-writeback)

## <a name="password-reset-registration"></a>Registrace resetování hesla

* **Otázka: je možné mé uživatelé registrovat svá vlastní data resetování hesla?**

  > **Odpověď:** Ano. Jak dlouho, dokud je povoleno obnovení hesla a udělení licence, mohou uživatelé portálu pro registraci resetování hesel (https://aka.ms/ssprsetup) k registraci jejich informace o ověřování. Uživatelé mohou také registrovat prostřednictvím přístupový Panel (http://myapps.microsoft.com). Pokud chcete zaregistrovat prostřednictvím panelu přístup, potřebují k výběru jejich profilový obrázek, vyberte **profil**a pak vyberte **zaregistrovat pro resetování hesla** možnost.
  >
  >
* **Otázka: Pokud lze povolit heslo resetovat pro skupinu a následně se rozhodnete ji zapnout pro každý jsou mé uživatelé požadované znovu zaregistrovat?**

  > **Odpověď:** Ne. Uživatelé, kteří mají vyplní data ověřování není nutné znovu zaregistrovat.
  >
  >
* **Otázka: je možné definovat data resetování hesel jménem Moje uživatelů?**

  > **Odpověď:** Ano, můžete tak učinit službou Azure AD Connect, prostředí PowerShell [portál Azure](https://portal.azure.com), nebo do centra pro správu Office 365. Další informace najdete v tématu [obnovit Data využívaná hesla pomocí samoobslužné služby Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: je možné synchronizovat data pro bezpečnostní otázky z místního?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka: je možné zaregistrovat vlastní uživatelé dat tak, že další uživatelé nemohou zobrazit tato data?**

  > **Odpověď:** Ano. Když uživatelé zaregistrovat dat pomocí heslo resetovat portálu pro registraci, uložení dat do pole privátní ověřování, které jsou viditelné pouze pro globální správce a uživatele.
  >
  >
* **Otázka: Moji uživatelé mají k registraci, aby mohli používat resetování hesla?**

  > **Odpověď:** Ne. Pokud definujete dostatek informací, ověřování jejich jménem, uživatelé nemají k registraci. Tak dlouho, dokud máte správně naformátován dat uložených v odpovídající pole v adresáři, resetování hesla funguje.
  >
  >
* **Otázka: je možné synchronizovat nebo nastavit telefon pro ověření, e-mailu ověřování nebo ověřování phone pole jménem Moje uživatelů?**

  > **Odpověď:** pole, která mohou být globální správce nastavit jsou definovány v článku [požadavky SSPR dat](howto-sspr-authenticationdata.md).
  >
  >
* **Otázka: jak na portál pro registraci určit, které možnosti zobrazíte Moji uživatelé?**

  > **Odpověď:** heslo resetovat registrační portál zobrazuje pouze možnosti, které jste povolili pro vaše uživatele. Tyto možnosti se nacházejí v části **zásady resetování hesel uživateli** svého adresáře **konfigurace** kartě. Například pokud nepovolíte bezpečnostní otázky, pak nebudou se uživatelé moct zaregistrovat pro tuto možnost.
  >
  >
* **Otázka: když se považuje za uživatele registrované?**

  > **Odpověď:** uživatel se považuje zaregistrovat pro SSPR, když registrace alespoň **několik metod, které jsou nutná k obnovení** heslo, které jste nastavili v [portál Azure](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetování hesla

* **Otázka: zabránit uživatelům ve více pokusí resetovat heslo v krátké době?**

  > **Odpověď:** Ano, jsou funkce zabezpečení, které jsou součástí pro ochranu před zneužitím pro vytvoření nového hesla. 
  >
  > Uživatelé mohou zkuste pouze pět resetování pokusů o zadání hesla v období 24 hodin, než se uzamčen po dobu 24 hodin. 
  >
  > Uživatele můžete zkusit ověřit telefonní číslo odeslat SMS a ověřit bezpečnostní otázky a odpovědi pouze pětkrát v rámci jednu hodinu, než jste uzamčena na dobu 24 hodin. 
  >
  > Uživatelé mohou odesílat e-mailu nesmí být delší než 10krát v rámci období 10 minut, než se uzamčen po dobu 24 hodin.
  >
  > Čítače se obnoví, jakmile uživatel vytvoří své heslo.
  >
  >
* **Otázka: jak dlouho má čekat pro příjem e-mailem, SMS nebo telefonní hovor z resetování hesla?**

  > **Odpověď:** e-mailů, zpráv SMS, a telefonních hovorů by měl doručení za minutu. Normální postup je 5 až 20 sekund.
    >Pokud nemáte přijímat oznámení v této časového rámce:
        > * Zkontrolujte své složky s nevyžádanou poštou.
        > * Zkontrolujte, že číslo nebo e-mailu kontaktovaný je ta, kterou očekáváte.
        > * Zkontrolujte, jestli je ověřování dat v adresáři správně naformátován, například 4255551234 + 1 nebo *user@contoso.com*. 
  >
  >
* **Otázka: jaké jazyky jsou podporovány resetování hesla?**

  > **Odpověď:** heslo resetovat uživatelského rozhraní, zpráv SMS a hlasové hovory jsou lokalizované do stejné jazyky, které jsou podporovány v Office 365.
  >
  >
* **Otázka: jaký částí prostředí resetování hesla získat značky po nastavení organizační značky položky v adresáře Moje aktivity karta Konfigurace?**

  > **Odpověď:** portálu pro resetování hesla zobrazuje logo vaší organizace a umožňuje vám nakonfigurovat na odkaz "Obraťte se na správce" tak, aby odkazovalo vlastní e-mailu nebo adresa URL. E-mailu, který odeslal resetování hesla zahrnuje loga vaší organizace, barvy a název v textu e-mailu a je přizpůsobit z nastavení pro tento konkrétní název.
  >
  >
* **Otázka: jak můžete naučit Moje uživatele o tom, kde přejděte k resetování hesla?**

  > **Odpověď:** vyzkoušíme některá doporučení v našich [SSPR nasazení](howto-sspr-deployment.md#email-based-rollout) článku.
  >
  >
* **Otázka: je možné pomocí této stránky z mobilního zařízení?**

  > **Odpověď:** Ano, tato stránka funguje na mobilních zařízeních.
  >
  >
* **Otázka: podporujete odemykání místní účty služby Active Directory při resetování hesel uživatelů?**

  > **Odpověď:** Ano. Pokud uživatel resetuje heslo, pokud byla nasazena zpětný zápis hesla přes Azure AD Connect, že uživatelský účet je automaticky odemknout při jejich obnovit své heslo.
  >
  >
* **Otázka: jak můžete integrovat přímo do daného uživatele plochy přihlašování resetování hesla?**

  > **Odpověď:** Pokud jste zákazník Azure AD Premium, můžete nainstalovat Microsoft Identity Manager bez dalších poplatků a nasazení v případě místních řešení resetování hesla.
  >
  >
* **Otázka: je možné nastavit různé bezpečnostní otázky pro různá národní prostředí?**

  > **Odpověď:** Ne, to není možné ještě dnes.
  >
  >
* **Otázka: jak mnoha dotazy můžete nakonfigurovat možnost ověřování otázky zabezpečení?**

  > **Odpověď:** můžete nakonfigurovat až 20 vlastní bezpečnostních otázek v [portál Azure](https://portal.azure.com).
  >
  >
* **Otázka: jak dlouho může bezpečnostní otázky být?**

  > **Odpověď:** bezpečnostní otázky může mít 3 až 200 znaků.
  >
  >
* **Otázka: jak dlouho může odpovědi na bezpečnostní otázky být?**

  > **Odpověď:** odpovědi mohou být 3 až 40 znaků.
  >
  >
* **Otázka: je duplicitní odpovědi na bezpečnostní otázky odmítl?**

  > **Odpověď:** Ano, jsme odmítnout duplicitní odpovědi na bezpečnostní otázky.
  >
  >
* **Otázka: je možné uživatel zaregistrovat na stejné bezpečnostní otázku více než jednou?**

  > **Odpověď:** Ne. Jakmile uživatel zaregistruje na určitou otázku, jejich nelze zaregistrovat tuto otázku ještě jednou.
  >
  >
* **Otázka: je možné nastavit minimální limit bezpečnostní otázky pro registraci a resetování?**

  > **Odpověď:** Ano, lze nastavit jeden limit pro registraci a druhý pro obnovení. Tři až pět bezpečnostní otázky, může být nutná pro registraci, a tři až pět dotazy, může být nutná pro obnovení.
  >
  >
* **Otázka: uživatel nakonfiguroval Moje zásady budou muset uživatelé použít bezpečnostní otázky pro resetování, ale Azure správci pravděpodobně nastavují různým způsobem.**

  > **Odpověď:** jedná se o očekávané chování. Microsoft vynucuje silné výchozí zásady resetování hesel dvě brány pro žádnou roli Správce služby Azure. To brání správci pomocí bezpečnostních otázek. Můžete najít další informace o této zásadě v [zásady hesel a omezení v Azure Active Directory](concept-sspr-policy.md#administrator-password-policy-differences) článku.
  >
  >
* **Otázka: Pokud uživatel má více než maximální počet otázek vyžadovaných k resetování registrované, jak jsou bezpečnostní otázky vybrané během obnovení?**

  > **A:** *N* počet bezpečnostní otázky jsou náhodně vybrané mimo celkový počet otázek, které má uživatel zaregistrován pro, kde *N* jde o dobu, která je nastavena **číslo počet otázek, které jsou nutná k obnovení** možnost. Například pokud uživatel zaregistroval pět bezpečnostní otázky, ale pouze tři nutných k obnovení hesla, tři, z pěti otázky jsou náhodně vybírány a jsou uvedené v resetování. Aby se zabránilo otázku ražením, pokud uživatel získá odpovědi na otázky nesprávný procesu výběru spustí přes.
  >
  >
* **Otázka: jak dlouho jsou e-mailu a SMS jednorázového hesla platný?**

  > **Odpověď:** dobu platnosti relace pro resetování hesla je 15 minut. Od začátku operace resetování hesla uživatel má 15 minut resetovat heslo. E-mailu a SMS jednorázové heslo jsou neplatné po vypršení tohoto časového období.
  >
  >
* **Otázka: je možné zablokovat uživatelům resetování hesla?**

  > **Odpověď:** Ano, pokud používáte skupiny umožňující SSPR, můžete odebrat jednotlivého uživatele ze skupiny, který umožňuje uživatelům obnovit své heslo. Pokud je uživatel globální správce zachovávají možnost Resetovat heslo, a to nelze zakázat.
  >
  >

## <a name="password-change"></a>Změna hesla

* **Otázka: kde by měli Moji uživatelé přejít ke změně hesla?**

  > **A:** uživatelé mohou změnit jejich hesla kdekoli uvidí jejich profilový obrázek nebo ikonu, jako je v pravém horním rohu jejich [Office 365](https://portal.office.com) portál nebo [přístupový Panel](https://myapps.microsoft.com) dojde. Uživatelé mohou změnit vlastní hesla ze [stránky profil Panel přístupu](https://account.activedirectory.windowsazure.com/r#/profile). Uživatelé mohou také vyzváni ke změně hesla na přihlašovací stránce služby Azure AD automaticky, pokud vypršela platnost hesla. Nakonec můžete vyhledat uživatele [portálu změn hesel Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) přímo Pokud chtějí změnit své heslo.
  >
  >
* **Otázka: je možné se svým uživatelům a upozornění na portálu Office, když vyprší platnost hesla pro místní?**

  > **Odpověď:** Ano, to je možné ještě dnes pomocí služby Active Directory Federation Services (AD FS). Pokud používáte službu AD FS, postupujte podle pokynů [odesílání deklarace identity zásady hesel se službou AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) článku. Pokud používáte synchronizaci hodnoty hash hesla, to není možné ještě dnes. Jsme nesynchronizují zásady pro hesla z místních adresářů, takže není možné, že nám odeslat oznámení o vypršení platnosti do prostředí cloudu. V obou případech je také možné [upozorněte uživatele, jejichž hesla se vyprší pomocí prostředí PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **Otázka: je možné blokovat uživatelům změnu hesla?**

  > **Odpověď:** pro uživatele, jenom pro cloud, nejde blokovat změny hesla. Pro místní uživatele, můžete nastavit **uživatel nemůže změnit heslo** možnost vybrat. Vybraní uživatelé nelze změnit své heslo.
  >
  >

## <a name="password-management-reports"></a>Sestavy správy hesel

* **Otázka: jak dlouho trvá pro data objeví na sestavy správy heslo?**

  > **Odpověď:** Data mají objevit na sestav správy hesel v 5 až 10 minut. V některých případech to může trvat až jednu hodinu, než se objeví.
  >
  >
* **Otázka: jak můžete filtrovat sestavy správy heslo?**

  > **Odpověď:** Pokud chcete filtrovat sestavy správy heslo, vyberte malé lupy extrémně napravo od popisky sloupců, do horní části sestavy. Pokud chcete provádět bohatší filtrování, můžete stáhnout sestavu do aplikace Excel a vytvoření kontingenční tabulky.
  >
  >
* **Otázka: co je maximální počet událostí, které jsou uložené v sestavy správy heslo?**

  > **Odpověď:** až než 75 000 heslo resetovat nebo heslo resetovat registrace události jsou uložené v sestavy správy heslo, pokrývání uzlů zpět Pokud je to 30 dní. Pracujeme na rozbalte tento počet na obsahovat další události.
  >
  >
* **Otázka: jak daleko zpět sestav správy hesel přejděte?**

  > **Odpověď:** Správa hesel sestavy zobrazit operace, které došlo k chybě během posledních 30 dnů. Teď Pokud budete potřebovat k archivaci tato data můžete stáhnout sestavy pravidelně a uložit je do samostatných umístění.
  >
  >
* **Otázka: je maximální počet řádků, které se mohou objevit v sestavy správy heslo?**

  > **Odpověď:** Ano. Nesmí být delší než 75 000 řádků se může zobrazit na buď sestavy správy heslo, ať už se zobrazují v uživatelském rozhraní nebo jsou staženy.
  >
  >
* **Otázka: je rozhraní API pro přístup k resetování hesla nebo registrační data pro vytváření sestav?**

  > **Odpověď:** Ano. Informace o přístupu k resetování hesla, vytváření sestav datového proudu, najdete v části [zjistěte, jak pro přístup k resetování hesla události vytváření sestav prostřednictvím kódu programu](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Zpětný zápis hesla

* **Otázka: jak zpětný zápis hesla funguje na pozadí?**

  > **Odpověď:** najdete v článku [jak zpětný zápis hesla funguje](howto-sspr-writeback.md) pro vysvětlení, co se stane, když zapnete zpětný zápis hesla a tok dat prostřednictvím systému zpět do místního prostředí.
  >
  >
* **Otázka: jak dlouho zpětný zápis hesla trvá fungovat? Je existuje synchronizaci zpoždění stejně, jako je se synchronizací hodnoty hash hesla?**

  > **Odpověď:** zpětný zápis hesla je rychlé. Je synchronní kanál, který funguje zásadně jinak než synchronizaci hodnoty hash hesla. Zpětný zápis hesla umožňuje uživatelům získat v reálném čase zpětnou vazbu o úspěšnosti jejich resetování hesla nebo změňte operaci. Průměrná doba pro úspěšné zpětný zápis hesla je v části 500 ms.
  >
  >
* **Otázka: Pokud je můj účet místní vypnutá, jak je můj účet cloudu a přístup vliv?**

  > **Odpověď:** Pokud vaše místní ID je zakázaná, ID cloudu a přístup budou rovněž zakázány při dalším intervalu synchronizaci přes Azure AD Connect. Ve výchozím nastavení je této synchronizace každých 30 minut.
  >
  >
* **Otázka: Pokud je omezené Můj účet místní zásady hesel místní služby Active Directory, SSPR orientují tuto zásadu po změně hesla?**

  > **Odpověď:** Ano, spoléhá na SSPR a dodržuje zásady hesel místní služby Active Directory. Tato zásada obsahuje typické zásady hesel domény služby Active Directory, stejně jako všechny zásady definované a podrobných heslo, které jsou zaměřeny na uživatele.
  >
  >
* **Otázka: jaký typy účtů zpětný zápis hesla funguje pro?**

  > **Odpověď:** funguje zpětný zápis hesla pro uživatelské účty, které jsou synchronizované z místní služby Active Directory do Azure AD, včetně federovaný, hodnoty hash hesla synchronizovány a předávací Autentication uživatelů.
  >
  >
* **Otázka: zpětný zápis hesla vynutit zásady hesel Moje doména?**

  > **Odpověď:** Ano. Zpětný zápis hesla vynucuje stáří hesla, historie, složitost, filtrů a další omezení, které můžete ukládat do místě hesla v místní doméně.
  >
  >
* **Otázka: je zpětný zápis hesla zabezpečení?  Jak se může být jistí, že nebude získat hacker I?**

  > **Odpověď:** Ano, jsou zabezpečené zpětný zápis hesla. Číst informace o čtyři vrstvy zabezpečení implementované službu zpětný zápis hesla, podívejte se [model zabezpečení zpětný zápis hesla](howto-sspr-writeback.md#password-writeback-security-model) v tématu [přehled zpětný zápis hesla](howto-sspr-writeback.md) článku.
  >
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
* [Jak hlásit aktivitu v SSPR?](howto-sspr-reporting.md)
* [Jaké jsou všechny možnosti v SSPR a co znamenají?](concept-sspr-howitworks.md)
* [Myslím, že je něco poškozené. Jak řešit problémy SSPR?](active-directory-passwords-troubleshoot.md)
