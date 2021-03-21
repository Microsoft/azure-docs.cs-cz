---
title: Řešení potíží s samoobslužným resetováním hesla – Azure Active Directory
description: Přečtěte si, jak řešit běžné problémy a postup řešení pro Samoobslužné resetování hesla ve službě Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103600858"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Řešení potíží s resetováním hesla samoobslužné služby v Azure Active Directory

Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD) umožňuje uživatelům resetovat hesla v cloudu.

Pokud máte problémy s SSPR, může vám pomáhat následující postup pro odstraňování potíží a běžné chyby. V tomto krátkém videu se můžete podívat i na to, [jak vyřešit šest nejčastějších chybových zpráv koncového uživatele SSPR](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1).

Pokud nemůžete najít odpověď na váš problém, [naši týmy podpory vám budou mít k dispozici stále](#contact-microsoft-support) více.

## <a name="sspr-configuration-in-the-azure-portal"></a>Konfigurace SSPR v Azure Portal

Pokud máte problémy se zobrazením nebo konfigurací možností SSPR v Azure Portal, přečtěte si následující postup řešení potíží:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>V Azure Portal se nezobrazuje oddíl **resetování hesla** v Azure AD.

Možnost nabídky **resetování hesla** se nezobrazí, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí.

Pokud chcete přiřadit licenci k příslušnému účtu správce, postupujte podle kroků k [přiřazení, ověřování a řešení potíží s licencemi](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Nezobrazuje se konkrétní možnost konfigurace.

Mnohé prvky uživatelského rozhraní jsou skryté, dokud je nebudete potřebovat. Než začnete hledat konkrétní možnosti konfigurace, ujistěte se, že je tato možnost povolená.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Nevidím kartu **integrace na místní úrovni** .

Místní zpětný zápis hesla je viditelný jenom v případě, že jste stáhli Azure AD Connect a nakonfigurovali funkci.

Další informace najdete v tématu [Začínáme s Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>Vytváření sestav SSPR

Pokud máte problémy s vytvářením sestav SSPR v Azure Portal, přečtěte si následující postup řešení potíží:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>V kategorii událostí auditu **Samoobslužná správa hesel** se nezobrazují žádné typy aktivit správy hesel.

K tomu může dojít, pokud nemáte licenci Azure AD přiřazenou správci, který tuto operaci provádí.

Pokud chcete přiřadit licenci k příslušnému účtu správce, postupujte podle kroků k [přiřazení, ověřování a řešení potíží s licencemi](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Registrace uživatelů se zobrazí víckrát.

V případě, že uživatel zaregistruje, aktuálně protokoluje každou jednotlivou datovou část, která je zaregistrována jako samostatná událost.

Pokud chcete tato data agregovat a máte větší flexibilitu v tom, jak si ji můžete zobrazit, můžete si ji stáhnout a data v Excelu otevřít jako kontingenční tabulku.

## <a name="sspr-registration-portal"></a>Portál pro registraci SSPR

Pokud vaši uživatelé mají problémy s registrací pro SSPR, přečtěte si následující postup řešení potíží:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Adresář není povolen pro resetování hesla. Uživateli se může zobrazit chyba s oznámením, že váš správce vám nepovolil použití této funkce.

SSPR můžete povolit pro všechny uživatele, žádné uživatele ani pro vybrané skupiny uživatelů. Pro SSPR se v tuto chvíli dá povolit jenom jedna skupina Azure AD pomocí Azure Portal. V rámci širšího nasazení SSPR se podporují vnořené skupiny. Ujistěte se, že uživatelé ve skupinách, které jste zvolili, mají přiřazené příslušné licence.

V Azure Portal změňte konfiguraci **Povolit Samoobslužné resetování hesla** na *vybrané* nebo *všechny* a pak vyberte **Uložit**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Uživatel nemá přiřazenou licenci Azure AD. Uživateli se může zobrazit chyba s oznámením, že váš správce vám nepovolil použití této funkce.

Pro SSPR se v tuto chvíli dá povolit jenom jedna skupina Azure AD pomocí Azure Portal. V rámci širšího nasazení SSPR se podporují vnořené skupiny. Ujistěte se, že uživatelé ve skupinách, které jste zvolili, mají přiřazené příslušné licence. Projděte si předchozí krok řešení potíží a povolte SSPR podle potřeby.

Projděte si také postup řešení potíží, abyste se ujistili, že správce, který provádí možnosti konfigurace, má přiřazenou licenci. Pokud chcete přiřadit licenci k příslušnému účtu správce, postupujte podle kroků k [přiřazení, ověřování a řešení potíží s licencemi](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Při zpracování požadavku došlo k chybě.

Obecné chyby registrace SSPR můžou být způsobené mnoha problémy, ale obecně Tato chyba je způsobená výpadkem služby nebo problémem s konfigurací. Pokud se tato obecná chyba bude dál zobrazovat při opakování procesu registrace SSPR, požádejte o další pomoc [podporu Microsoftu](#contact-microsoft-support) .

## <a name="sspr-usage"></a>Využití SSPR

Pokud vy nebo vaši uživatelé máte problémy s používáním SSPR, přečtěte si následující scénáře řešení potíží a postup řešení:

| Chyba | Řešení |
| --- | --- |
| Adresář není povolen pro resetování hesla. | V Azure Portal změňte konfiguraci **Povolit Samoobslužné resetování hesla** na *vybrané* nebo *všechny* a pak vyberte **Uložit**. |
| Uživatel nemá přiřazenou licenci Azure AD. | Tato situace může nastat, pokud nemáte přiřazenou licenci Azure AD k požadovanému uživateli. Pokud chcete přiřadit licenci k příslušnému účtu správce, postupujte podle kroků k [přiřazení, ověřování a řešení potíží s licencemi](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| V adresáři je povoleno resetování hesla, ale uživatel má chybějící nebo poškozené ověřovací informace. | Ujistěte se, že uživatel má správně vytvořené kontaktní údaje pro soubor v adresáři. Další informace najdete v tématu [data používaná samoobslužným resetováním hesla služby Azure AD](howto-sspr-authenticationdata.md). |
| V adresáři je povoleno resetování hesla, ale pokud je zásada nastavená tak, aby vyžadovala dvě metody ověřování, má uživatel pouze jedno kontaktní údaje v souboru. | Ujistěte se, že uživatel má alespoň dvě správně nakonfigurované metody kontaktu. Příkladem je číslo mobilního telefonu *i* telefon do kanceláře. |
| Pro resetování hesla je povolený adresář a uživatel je správně nakonfigurovaný, ale uživatel není schopen kontaktovat. | Může to být výsledkem dočasné chyby služby nebo pokud jsou nesprávná kontaktní data, která nepůjde správně zjistit. <br> <br> Pokud uživatel počká 10 sekund, zobrazí se odkaz "zkusit znovu" a "kontaktujte správce". Pokud uživatel vybere "zkusit znovu", znovu se pokusí zavolat. Pokud uživatel vybere "kontaktujte správce", pošle mu e-mail s žádostí o resetování hesla, které se má pro tento uživatelský účet provést. |
| Uživatel nikdy neobdrží resetování hesla SMS nebo telefonní hovor. | Může to být výsledkem poškozeného telefonního čísla v adresáři. Ujistěte se, že je telefonní číslo ve formátu "+ 1 4251234567". <br> <br>Resetování hesla nepodporuje rozšíření, a to ani v případě, že ho zadáte v adresáři. Rozšíření jsou před uskutečněním volání odstraněna. Použijte číslo bez přípony nebo integrujte rozšíření do telefonního čísla ve vaší privátní větvi Exchange (PBX). |
| Uživatel nikdy neobdrží e-mail pro resetování hesla. | Nejběžnější příčinou tohoto problému je, že zpráva je odmítnutá filtrem spamu. U e-mailu ověřte složku s nevyžádanou poštou, Nevyžádaná pošta nebo odstraněnou položku. <br> <br> Ujistěte se také, že uživatel kontroluje správný e-mailový účet registrovaný v SSPR. |
| Nastavil (a) jsem zásady pro resetování hesel, ale pokud účet správce používá resetování hesla, tato zásada se nepoužije. | Společnost Microsoft spravuje a řídí zásady resetování hesel správce, aby bylo zajištěno nejvyšší úroveň zabezpečení. |
| Uživateli se zabrání, aby se resetování hesla příliš mnohokrát za den. | Mechanismus automatického omezování se používá k zablokování uživatelů při pokusu o resetování hesel příliš mnohokrát v krátké době. K omezování dochází v následujících situacích: <br><ul><li>Uživatel se snaží ověřit telefonní číslo pětkrát za hodinu.</li><li>Uživatel se pokusí použít bránu bezpečnostních otázek pětkrát za hodinu.</li><li>Uživatel se několikrát pokusí resetovat heslo pro stejný uživatelský účet pětkrát za hodinu.</li></ul>Pokud uživatel narazí na tento problém, musí počkat 24 hodin od posledního pokusu. Uživatel pak může resetovat heslo. |
| Uživatel uvidí chybu při ověřování jejich telefonního čísla. | K této chybě dochází, pokud zadané telefonní číslo neodpovídá telefonnímu číslu v souboru. Ujistěte se, že uživatel zadává úplné telefonní číslo, včetně kódu oblasti a země, při pokusu o použití metody pro resetování hesla na telefonu. |
| Při zpracování požadavku došlo k chybě. | Obecné chyby registrace SSPR můžou být způsobené mnoha problémy, ale obecně Tato chyba je způsobená výpadkem služby nebo problémem s konfigurací. Pokud se tato obecná chyba bude dál zobrazovat při opakování procesu registrace SSPR, požádejte o další pomoc [podporu Microsoftu](#contact-microsoft-support) . |
| Porušení místních zásad | Heslo nesplňuje místní zásady hesel služby Active Directory. Uživatel musí definovat heslo, které splňuje požadavky na složitost nebo sílu. |
| Heslo nevyhovuje zásadám s fuzzy logikou. | Heslo, které se použilo, se zobrazí v [seznamu zakázaných hesel](./concept-password-ban-bad.md#how-are-passwords-evaluated) a nedá se použít. Uživatel musí definovat heslo, které splňuje nebo překračuje zásadu seznamu zakázaných hesel. |

## <a name="sspr-errors-that-a-user-might-see"></a>SSPR chyby, které může uživatel zobrazit

Jako součást procesu SSPR mohou být uživateli zobrazeny následující chyby a technické podrobnosti. Často se chyba nejedná o něco, co se může vyřešit, protože funkce SSPR musí pro svůj účet povolená, nakonfigurovaná nebo registrovaná.

Následující informace použijte k pochopení problému a o tom, co je potřeba opravit v tenantovi Azure AD nebo jednotlivém uživatelském účtu.

| Chyba | Podrobnosti | Technické podrobnosti |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce zakázal resetování hesla ve vaší organizaci. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se prosím na správce a požádejte ho, aby tuto funkci povolil.<br /><br />Další informace najdete v [nápovědě, zapomenutí hesla služby Azure AD](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: zjistili jsme, že správce nepovolil resetování hesla. Kontaktujte prosím správce a požádejte ho, aby pro vaši organizaci povolil resetování hesla. |
| WritebackNotEnabled = 10 |Je nám líto, ale heslo teď nemůžete resetovat, protože správce pro vaši organizaci nepovolil potřebnou službu. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby zkontroloval konfiguraci vaší organizace.<br /><br />Další informace o této potřebné službě najdete v tématu [Konfigurace zpětného zápisu hesla](./tutorial-enable-sspr-writeback.md). | SSPR_0010: zjistili jsme, že zpětný zápis hesla nebyl povolen. Kontaktujte prosím správce a požádejte ho, aby povolil zpětný zápis hesla. |
| SsprNotEnabledInUserPolicy = 11 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce nenakonfiguroval resetování hesla pro vaši organizaci. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se na správce a požádejte ho o konfiguraci resetování hesla.<br /><br />Další informace o konfiguraci resetování hesel najdete v tématu [rychlý Start: Samoobslužné resetování hesla služby Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: vaše organizace nedefinovala zásady pro resetování hesla. Kontaktujte prosím správce a požádejte ho, aby definoval zásady pro resetování hesla. |
| UserNotLicensed = 12 | Je nám líto, ale heslo teď nemůžete resetovat, protože ve vaší organizaci chybí požadované licence. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby zkontroloval přiřazení licence.<br /><br />Další informace o licencování najdete v tématu [požadavky na licencování pro Samoobslužné resetování hesla služby Azure AD](./concept-sspr-licensing.md). | SSPR_0012: vaše organizace nemá požadované licence potřebné k provedení resetování hesla. Kontaktujte prosím správce a požádejte ho, aby zkontroloval přiřazení licencí. |
| UserNotMemberOfScopedAccessGroup = 13 | Je nám líto, ale heslo teď nemůžete resetovat, protože správce nenakonfiguroval váš účet na používání resetování hesla. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby si nakonfigurovali svůj účet pro resetování hesla.<br /><br />Další informace o konfiguraci účtu pro resetování hesla najdete v tématu [zavedení resetování hesel pro uživatele](./howto-sspr-deployment.md). | SSPR_0013: nejste členem skupiny, která je povolená pro resetování hesla. Obraťte se na správce a žádost o přidání do skupiny. |
| UserNotProperlyConfigured = 14 | Je nám líto, ale heslo teď nemůžete resetovat, protože ve vašem účtu chybí potřebné informace. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Kontaktujte prosím správce a požádejte ho, aby vám heslo resetoval. Po opětovném přístupu k účtu budete muset zaregistrovat potřebné informace.<br /><br />Chcete-li zaregistrovat informace, postupujte podle kroků v článku [Registrace pro Samoobslužné resetování hesla](../user-help/active-directory-passwords-reset-register.md) . | SSPR_0014: k resetování hesla je potřeba další bezpečnostní údaje. Pokud chcete pokračovat, obraťte se na správce a požádejte ho, aby heslo resetoval. Po přístupu k účtu můžete zaregistrovat další bezpečnostní údaje na adrese https://aka.ms/ssprsetup . Správce může do svého účtu přidat další informace o zabezpečení, a to pomocí kroků uvedených v části [nastavení a čtení dat ověřování pro resetování hesla](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Je nám líto, ale v tuto chvíli nemůžeme resetovat vaše heslo kvůli problému s konfigurací resetování hesel vaší organizace. Neexistuje žádná další akce, kterou můžete použít k vyřešení této situace. Obraťte se prosím na správce a požádejte ho, aby prošetřil. <br /><br />Nebo<br /><br />V tuto chvíli nemůžeme vaše heslo resetovat kvůli problému s konfigurací resetování hesel vaší organizace. Není k dispozici žádná další akce, kterou můžete použít k vyřešení tohoto problému. Obraťte se prosím na správce a požádejte ho, aby prošetřil.<br /><br />Další informace o potenciálním problému najdete v tématu [řešení potíží se zpětným zápisem hesla](troubleshoot-sspr-writeback.md). | SSPR_0029: nemůžeme resetovat heslo z důvodu chyby v místní konfiguraci. Obraťte se prosím na správce a požádejte ho, aby prošetřil. |
| OnPremisesConnectivityError = 30 | Je nám líto, ale v tuto chvíli nemůžeme resetovat vaše heslo kvůli problémům s připojením k vaší organizaci. Neexistuje žádná akce, která by se měla provést, ale pokud to zkusíte znovu, zkuste problém vyřešit. Pokud se problém opakuje, obraťte se prosím na správce a požádejte ho, aby prošetřil.<br /><br />Další informace o problémech s připojením najdete v tématu [řešení potíží s připojením ke zpětnému zápisu hesla](troubleshoot-sspr-writeback.md). | SSPR_0030: nemůžeme resetovat vaše heslo z důvodu špatného připojení k místnímu prostředí. Obraťte se na správce a požádejte ho o prošetření.|

## <a name="azure-ad-forums"></a>Fóra Azure AD

Pokud máte obecné dotazy týkající se služby Azure AD a samoobslužného resetování hesla, můžete požádat komunitu o pomoc na [stránce s dotazem Microsoft Q&pro Azure Active Directory](/answers/topics/azure-active-directory.html). Mezi členy komunity patří inženýri, správci produktů, odborníky MVP a odborníci na IT.

## <a name="contact-microsoft-support"></a>Obraťte se na podporu Microsoftu

Pokud nemůžete najít odpověď na problém, naši týmy podpory vám budou mít k dispozici vždycky.

Abychom vám mohli správně pomoct, požádáme o to, abyste při otevírání případu zadali co nejvíc podrobností. Mezi tyto podrobnosti patří následující:

* **Obecný popis chyby**: co je to chyba? Jak se jednalo o chování, které se zaznamenalo? Jak můžeme chybu reprodukována? Poskytněte co nejvíce podrobností.
* **Stránka**: na kterou stránku jste se zaznamenali, když jste si všimli chybu? Pokud jste schopni a snímek obrazovky stránky, zahrňte adresu URL.
* **Kód podpory**: Jaký byl kód podpory, který byl vygenerován při uživateli, který chybu viděl?
   * Chcete-li najít tento kód, reprodukování chyby a potom vyberte odkaz na **Kód podpory** v dolní části obrazovky a odešlete pracovníkovi podpory identifikátor GUID, který je výsledkem.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Kód podpory je umístěný v pravém dolním rohu okna webového prohlížeče.":::

  * Pokud se nacházíte na stránce bez kódu podpory v dolní části, vyberte F12, vyhledejte identifikátor SID a CID a odešlete tyto dva výsledky technickému pracovníkovi podpory.
* **Datum, čas a časové pásmo**: zahrnuje přesné datum a čas v *časovém pásmu* , ke kterému došlo k chybě.
* **ID uživatele**: kdo byl uživateli, který tuto chybu viděl? Příkladem je *\@ contoso.com uživatele*.
   * Je tento Federovaný uživatel?
   * Je tento uživatel předávacího ověřování?
   * Je toto heslo – synchronizovaný uživatel – hash?
   * Je tento uživatel výhradně pro Cloud?
* **Licencování**: má uživatel přiřazenou licenci Azure AD?
* **Protokol událostí aplikace**: Pokud používáte zpětný zápis hesla a chyba se nachází ve vaší místní infrastruktuře, zahrňte do Azure AD Connect serveru kopii protokolu událostí aplikace.

## <a name="next-steps"></a>Další kroky

Další informace o SSPR najdete v článku [jak to funguje: Samoobslužné resetování hesla Azure AD](concept-sspr-howitworks.md) nebo [Jak funguje zpětný zápis hesla samoobslužného resetování hesla ve službě Azure AD?](concept-sspr-writeback.md).
