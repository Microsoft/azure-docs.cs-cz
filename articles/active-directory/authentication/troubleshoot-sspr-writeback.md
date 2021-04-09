---
title: Řešení potíží se zpětným zápisem hesla pro samoobslužnou službu – Azure Active Directory
description: Přečtěte si, jak řešit běžné problémy a postup řešení pro zpětný zápis resetování hesla pro samoobslužnou službu v Azure Active Directory
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
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682233"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Řešení potíží se zpětným zápisem pro Samoobslužné resetování hesla ve službě Azure Active Directory

Samoobslužné resetování hesla (SSPR) Azure Active Directory (Azure AD) umožňuje uživatelům resetovat hesla v cloudu. Zpětný zápis hesla je funkce povolená u [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , která umožňuje v reálném čase zapisovat změny hesel v cloudu zpátky do stávajícího místního adresáře.

Pokud máte problémy se zpětným zápisem SSPR, může vám pomáhat následující postup pro odstraňování potíží a běžné chyby. Pokud nemůžete najít odpověď na váš problém, [naši týmy podpory vám budou mít k dispozici stále](#contact-microsoft-support) více.

## <a name="troubleshoot-connectivity"></a>Řešení potíží s připojením

Pokud máte problémy se zpětným zápisem hesla pro Azure AD Connect, Projděte si následující kroky, které mohou problém vyřešit. Pro obnovení služby doporučujeme, abyste provedli následující kroky v uvedeném pořadí:

* [Potvrzení připojení k síti](#confirm-network-connectivity)
* [Restartujte službu Azure AD Connect Sync.](#restart-the-azure-ad-connect-sync-service)
* [Zakázat a znovu povolit funkci zpětného zápisu hesla](#disable-and-re-enable-the-password-writeback-feature)
* [Nainstalovat nejnovější verzi Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Řešení potíží se zpětným zápisem hesla](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Potvrzení připojení k síti

Nejběžnějším bodem selhání je to, že nejsou správně nakonfigurované porty brány firewall nebo proxy nebo nečinné prodlevy.

U Azure AD Connect verze *verze 1.1.443.0* a vyšší se vyžaduje přístup k *odchozímu https* pro následující adresy:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

[Koncové body Azure gov](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers):

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Pokud potřebujete více členitosti, přečtěte si [Seznam rozsahů IP adres datového centra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Tento seznam se aktualizuje v každé středu a vstoupí v platnost příští pondělí.

Další informace najdete v tématu [požadavky na připojení pro Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Restartujte službu Azure AD Connect Sync.

Chcete-li vyřešit potíže s připojením nebo jiné přechodné problémy se službou, proveďte následující kroky pro restartování služby Azure AD Connect Sync:

1. Jako správce na serveru, na kterém běží Azure AD Connect vyberte **Spustit**.
1. Do vyhledávacího pole zadejte *Services. msc* a vyberte **ENTER**.
1. Vyhledejte položku *Microsoft Azure AD synchronizace* .
1. Klikněte pravým tlačítkem myši na položku služby, vyberte možnost **restartovat** a počkejte na dokončení operace.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="Restartování služby Azure AD Sync pomocí grafického uživatelského rozhraní" border="false":::

Tyto kroky znovu navažte připojení ke službě Azure AD a měli byste vyřešit problémy s připojením.

Pokud restartování služby Azure AD Connect Sync nevyřeší váš problém, zkuste zakázat a znovu povolit funkci zpětného zápisu hesla v další části.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Zakázat a znovu povolit funkci zpětného zápisu hesla

Pokud chcete pokračovat v odstraňování potíží, proveďte následující kroky a zakažte a znovu povolte funkci zpětného zápisu hesla:

1. Jako správce na serveru, na kterém běží Azure AD Connect, otevřete **průvodce Azure AD Connect konfigurace**.
1. V **připojení ke službě Azure AD** zadejte svoje přihlašovací údaje globálního správce služby Azure AD.
1. V **Služba AD DS připojit k** zadejte přihlašovací údaje místního Active Directory Domain Services správce.
1. V části **jedinečně identifikující vaše uživatele** vyberte tlačítko **Další** .
1. V části **volitelné funkce** zrušte zaškrtnutí políčka **zpětný zápis hesla** .
1. Na zbývajících stránkách dialogového okna vyberte **Další** beze změny, dokud se nedostanete na stránku **připraveno ke konfiguraci** .
1. Ověřte, že je na **stránce připraveno ke konfiguraci** zobrazená možnost *zpětného zápisu hesla* , která je *zakázaná*. Kliknutím na zelené tlačítko **Konfigurovat** potvrďte provedené změny.
1. V části **dokončeno** zrušte zaškrtnutí možnosti **synchronizovat nyní** a potom kliknutím na tlačítko **Dokončit** zavřete průvodce.
1. Znovu otevřete **Průvodce konfigurací Azure AD Connect**.
1. Opakujte kroky 2-8, tentokrát vyberte možnost *zpětný zápis hesla* na stránce **volitelné funkce** a službu znovu povolte.

Tyto kroky znovu navažte připojení ke službě Azure AD a měli byste vyřešit problémy s připojením.

Pokud zakážete a pak znovu povolíte funkci zpětného zápisu hesla, přeinstalujte Azure AD Connect v další části.

### <a name="install-the-latest-azure-ad-connect-release"></a>Nainstalovat nejnovější verzi Azure AD Connect

Přeinstalace Azure AD Connect může vyřešit problémy s konfigurací a připojením mezi Azure AD a místním prostředím Active Directory Domain Services. Tento krok doporučujeme provést až po provedení předchozích kroků k ověření a řešení potíží s připojením.

> [!WARNING]
> Pokud jste přizpůsobili předem připravená pravidla synchronizace, *před pokračováním v upgradu je zálohujte a pak je po dokončení ručně znovu nasaďte.*

1. Stáhněte si nejnovější verzi Azure AD Connect z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Jak už jste nainstalovali Azure AD Connect, proveďte místní upgrade a aktualizujte instalaci Azure AD Connect na nejnovější verzi.

    Spusťte stažený balíček a podle pokynů na obrazovce aktualizujte Azure AD Connect.

Tyto kroky by měly znovu navázat spojení se službou Azure AD a vyřešit problémy s připojením.

Pokud instalace nejnovější verze serveru Azure AD Connect nevyřešila váš problém, zkuste zakázat a znovu povolit zpětný zápis hesla jako poslední krok po instalaci nejnovější verze.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Ověřte, že Azure AD Connect má požadovaná oprávnění.

Azure AD Connect vyžaduje pro zpětný zápis hesla služba AD DS **resetování hesla** . Pokud chcete zjistit, jestli má Azure AD Connect požadované oprávnění pro daný místní služba AD DS uživatelský účet, použijte funkci **Windows efektivní oprávnění** :

1. Přihlaste se k serveru Azure AD Connect a spusťte **Synchronization Service Manager** výběrem možnosti **Spustit**  >  **synchronizaci služby**.
1. Na kartě **konektory** vyberte konektor on-premises **Active Directory Domain Services** a pak vyberte **vlastnosti**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Synchronization Service Manager zobrazení úprav vlastností" border="false":::
  
1. V automaticky otevíraném okně vyberte **připojit k doménové struktuře služby Active Directory** a poznamenejte si vlastnost **uživatelské jméno** . Tato vlastnost je účet služba AD DS, který Azure AD Connect používá k provedení synchronizace adresářů.

    Aby Azure AD Connect mohl provést zpětný zápis hesla, musí mít účet služba AD DS oprávnění resetovat heslo. Oprávnění k tomuto uživatelskému účtu zkontrolujete v následujících krocích.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Hledání uživatelského účtu služby Active Directory služby synchronizace" border="false":::
  
1. Přihlaste se k místnímu řadiči domény a spusťte aplikaci **Uživatelé a počítače služby Active Directory** .
1. Vyberte **zobrazení** a ujistěte se, že je povolená možnost **Pokročilé funkce** .  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Uživatelé a počítače služby Active Directory zobrazují pokročilé funkce" border="false":::
  
1. Vyhledejte služba AD DS uživatelský účet, který chcete ověřit. Klikněte pravým tlačítkem na název účtu a vyberte **vlastnosti**.  
1. V automaticky otevíraném okně přejdete na kartu **zabezpečení** a vyberte **Upřesnit**.  
1. V místním okně **Upřesnit nastavení zabezpečení pro správce** přejděte na kartu **efektivní přístup** .
1. Zvolte **Vybrat uživatele**, vyberte účet služba AD DS používaný v Azure AD Connect a pak vyberte **Zobrazit efektivní přístup**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Karta platného přístupu zobrazující účet synchronizace" border="false":::
  
1. Posuňte se dolů a vyhledejte **heslo pro resetování**. Pokud má položka značku zaškrtnutí, má účet služba AD DS oprávnění resetovat heslo vybraného uživatelského účtu služby Active Directory.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Ověřuje se, jestli má účet synchronizace oprávnění resetovat heslo." border="false":::

## <a name="common-password-writeback-errors"></a>Běžné chyby zpětného zápisu hesla

Při zpětném zápisu hesla se můžou vyskytnout následující problémy, které jsou konkrétnější. Pokud máte jednu z těchto chyb, zkontrolujte navržené řešení a zkontrolujte, jestli zpětný zápis hesla funguje správně.

| Chyba | Řešení |
| --- | --- |
| Služba resetování hesla se nespustí místně. V protokolu událostí aplikace Azure AD Connectho počítače se zobrazí chyba 6800. <br> <br> Po zaregistrování, federovaném, předávacím ověřování nebo heslem synchronizovaným uživatelům nejde resetovat hesla. | Když je povolen zpětný zápis hesla, synchronizační modul zavolá knihovnu zpětného zápisu, aby provedl konfiguraci (připojování) tím, že komunikuje se službou clouding pro registraci. Jakékoli chyby, ke kterým došlo během připojování nebo spuštění koncového bodu služby Windows Communication Foundation (WCF) pro zpětný zápis hesla, mají za následek chyby v protokolu událostí v počítači Azure AD Connect. <br> <br> Při restartování služby Azure AD Sync (ADSync) se při konfiguraci zpětného zápisu spustí koncový bod WCF. Pokud ale po spuštění koncového bodu dojde k chybě, protokoluje se událost 6800 a služba synchronizace se spustí. Přítomnost této události znamená, že se koncový bod zpětného zápisu hesla nespustil. Podrobnosti protokolu událostí pro událost 6800 společně s položkami protokolu událostí generovanými komponentou PasswordResetService určují, proč nelze koncový bod spustit. Zkontrolujte tyto chyby protokolu událostí a pokuste se restartovat Azure AD Connect, pokud zpětný zápis hesla stále nefunguje. Pokud potíže potrvají, zkuste zakázat a znovu povolit zpětný zápis hesla.
| Když se uživatel pokusí resetovat heslo nebo odemknout účet se zapnutým zpětným zápisem hesla, operace se nezdařila. <br> <br> Kromě toho se v protokolu událostí Azure AD Connect, který obsahuje, zobrazí událost s informacemi o tom, že synchronizační modul vrátil chybu HR = 800700CE, zpráva = název souboru nebo přípona je moc dlouhá, až nastane operace odemknutí. | Vyhledejte účet služby Active Directory pro Azure AD Connect a resetujte heslo, aby neobsahovalo více než 256 znaků. V dalším kroku otevřete **synchronizační službu** z nabídky **Start** . Přejděte ke **konektorům** a najděte **konektor služby Active Directory**. Vyberte ji a pak vyberte **vlastnosti**. Přejděte na stránku **přihlašovací údaje** a zadejte nové heslo. Kliknutím na **tlačítko OK** stránku zavřete. |
| V posledním kroku procesu instalace Azure AD Connect se zobrazí chyba s oznámením, že se nepodařilo nakonfigurovat zpětný zápis hesla. <br> <br> Protokol událostí aplikace Azure AD Connect obsahuje chybu 32009 s textem "Chyba při získávání ověřovacího tokenu". | K této chybě dochází v následujících dvou případech: <br><ul><li>Zadali jste nesprávné heslo pro účet globálního správce, který jste zadali na začátku procesu instalace Azure AD Connect.</li><li>Pokusili jste se použít federovaného uživatele pro účet globálního správce zadaný na začátku procesu instalace Azure AD Connect.</li></ul> Chcete-li tento problém vyřešit, ujistěte se, že nepoužíváte federační účet pro globálního správce zadaný na začátku procesu instalace a zda je zadané heslo správné. |
| Protokol událostí Azure AD Connect počítače obsahuje chybu 32002, která je vyvolána spuštěním PasswordResetService. <br> <br> Chyba čtení: "Chyba při připojování k ServiceBus. Poskytovatel tokenů nemohl poskytnout token zabezpečení. " | Vaše místní prostředí se nemůže připojit ke koncovému bodu Azure Service Bus v cloudu. Tato chyba je obvykle způsobená pravidlem brány firewall blokujícím odchozí připojení ke konkrétnímu portu nebo webové adrese. Další informace najdete v tématu [požadavky na připojení](../hybrid/how-to-connect-install-prerequisites.md) . Po aktualizaci těchto pravidel restartujte server Azure AD Connect a zpětný zápis hesla by měl začít znovu pracovat. |
| Po nějaké době se nemůžou resetovat hesla uživatelů, kteří se synchronizovaly, předávací ověřování nebo hesla-hash synchronizující s heslem. | Ve výjimečných případech se služba zpětného zápisu hesla může Azure AD Connect po restartování nedaří restartovat. V těchto případech nejdřív ověřte, jestli je zpětný zápis hesla povolený místně. Můžete to ověřit pomocí Průvodce Azure AD Connect nebo PowerShellu. Pokud se zdá, že je tato funkce povolená, zkuste povolit nebo zakázat funkci znovu buď. Pokud tento krok řešení potíží nefunguje, zkuste dokončit odinstalaci a přeinstalaci Azure AD Connect. |
| Federované, předávací ověřování nebo hesla synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokusí resetovat hesla, uvidí chybu po pokusu o odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br ><br> Kromě tohoto problému se při operacích resetování hesla může zobrazit chyba, že agentovi pro správu byl odepřen přístup v místních protokolech událostí. | Pokud se v protokolu událostí zobrazují tyto chyby, zkontrolujte, že účet agenta pro správu služby Active Directory (ADMA), který jste zadali v průvodci v době konfigurace, má potřebná oprávnění ke zpětnému zápisu hesla. <br> <br> Po zadání tohoto oprávnění může trvat až jednu hodinu, než se oprávnění trickle prostřednictvím `sdprop` úlohy na pozadí na řadiči domény (DC). <br> <br> Aby bylo resetování hesla správné, musí být oprávnění označeno popisovačem zabezpečení objektu uživatele, jehož heslo Probíhá resetování. Dokud se toto oprávnění nebude zobrazovat u objektu uživatele, resetování hesla se nadále nebude podařit se zprávou o odepření přístupu. |
| Federované, předávací ověřování nebo hesla, která jsou synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokoušejí resetovat hesla, se zobrazí chyba po odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému se při operacích resetování hesla může v protokolech událostí ve službě Azure AD Connect zobrazit chyba s informacemi o tom, že se nepovedlo najít objekt. | Tato chyba obvykle indikuje, že synchronizační modul nemůže najít objekt uživatele v prostoru konektoru služby Azure AD nebo propojený objekt úložiště úložiště služby Azure AD. <br> <br> Pokud chcete tento problém vyřešit, ujistěte se, že je uživatel skutečně synchronizovaný z místního prostředí do Azure AD prostřednictvím aktuální instance Azure AD Connect a kontroluje stav objektů v prostorech konektoru a v MV. Ověřte, zda je objekt služby Active Directory Certificate Services (AD CS) připojen k objektu MV prostřednictvím pravidla "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federované, předávací ověřování nebo hesla synchronizovaná pomocí algoritmu hash pro uživatele, kteří se pokusí resetovat hesla, uvidí chybu po odeslání hesla. Chyba indikuje, že došlo k potížím se službou. <br> <br> Kromě tohoto problému se během operací resetování hesla může v protokolech událostí ve službě Azure AD Connect zobrazit chyba s informacemi o tom, že se našla zpráva s více shodami. | To znamená, že synchronizační modul zjistil, že je objekt MV připojen k více než jednomu objektu služby AD CS prostřednictvím "Microsoft.InfromADUserAccountEnabled.xxx". To znamená, že uživatel má povolený účet ve více než jedné doménové struktuře. Tento scénář není podporován pro zpětný zápis hesla. |
| Operace hesla selžou s chybou konfigurace. Protokol událostí aplikace obsahuje Azure AD Connect chyba 6329 s textem "0x8023061f (operace se nezdařila, protože synchronizace hesla není u tohoto agenta pro správu povolena") ". | K této chybě dojde, pokud je konfigurace Azure AD Connect změněna tak, aby přidala novou doménovou strukturu služby Active Directory (nebo pro odebrání a čtení existující doménové struktury) poté, co byla funkce zpětného zápisu hesla již povolena. Operace s heslem pro uživatele v těchto nedávno přidaných doménových strukturách selžou. Chcete-li tento problém vyřešit, zakažte a znovu povolte funkci zpětného zápisu hesla po dokončení změn konfigurace doménové struktury. |

## <a name="password-writeback-event-log-error-codes"></a>Kódy chyb protokolu událostí pro zpětný zápis hesla

Osvědčeným postupem při odstraňování potíží se zpětným zápisem hesla je kontrola protokolu událostí aplikace na počítači Azure AD Connect. Tento protokol událostí obsahuje události ze dvou zdrojů pro zpětný zápis hesla. Zdroj *PasswordResetService* popisuje operace a problémy související s operacemi zpětného zápisu hesla. Zdroj *AdSync* popisuje operace a problémy související s nastavením hesel v prostředí Active Directory Domain Services.

### <a name="if-the-source-of-the-event-is-adsync"></a>Pokud je zdrojem události ADSync

| Kód | Název nebo zpráva | Description |
| --- | --- | --- |
| 6329 | BAIL: MMS (4924) 0x80230619: "omezení brání změně hesla na aktuálně zadaný." | K této události dojde, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje stáří hesla, historii, složitost nebo požadavky na filtrování domény. <br> <br> Pokud máte minimální stáří hesla a v časovém intervalu jste nedávno změnili heslo, nebudete moct znovu změnit heslo, dokud nedosáhne zadaného stáří ve vaší doméně. Pro účely testování musí být minimální stáří nastavené na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které se nepoužilo v posledních *n* časech, kde *N* je nastavení historie hesel. Pokud vyberete heslo, které se v posledních *N* časech používalo, zobrazí se v tomto případě chyba. Pro účely testování by měla být historie hesel nastavená na 0. <br> <br> Pokud máte požadavky na složitost hesla, budou všechny tyto zásady vynutily, když se uživatel pokusí změnit nebo resetovat heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdařila. |
| 6329 | MMS (3040): admaexport. cpp (2837): Server neobsahuje ovládací prvek zásad hesel LDAP. | K tomuto problému dochází, pokud se na řadičích domény nepovoluje LDAP_SERVER_POLICY_HINTS_OID Control (1.2.840.113556.1.4.2066). Chcete-li použít funkci zpětného zápisu hesla, je nutné povolit ovládací prvek. K tomu je potřeba, aby řadiče domény byly na Windows serveru 2008 R2 nebo novějším. |
| HR 8023042 | Synchronizační modul vrátil chybu HR = 80230402, zpráva = pokus o získání objektu se nezdařil, protože existují duplicitní položky se stejnou kotvou. | K této chybě dochází, pokud je ve více doménách povoleno stejné ID uživatele. Příkladem je, že synchronizujete účet a doménové struktury prostředků a máte v každé doménové struktuře k dispozici stejné ID uživatele a povoleno. <br> <br> K této chybě může dojít také v případě, že použijete nejedinečný atribut kotvy, jako je například alias nebo hlavní název uživatele (UPN), a dva uživatelé sdílejí stejný atribut ukotvení. <br> <br> Chcete-li tento problém vyřešit, ujistěte se, že nemáte žádné duplicity uživatelů v rámci vašich domén a že pro každého uživatele použijete jedinečný atribut Anchor. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Pokud je zdrojem události PasswordResetService

| Kód | Název nebo zpráva | Description |
| --- | --- | --- |
| 31001 | PasswordResetStart | Tato událost označuje, že místní služba zjistila požadavek na resetování hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnotu hash hesla, které pocházejí z cloudu. Tato událost představuje první událost při každé operaci zpětného zápisu hesla a obnovení. |
| 31002 | PasswordResetSuccess | Tato událost označuje, že uživatel během operace resetování hesla vybral nové heslo. Zjistili jsme, že toto heslo splňuje požadavky na heslo společnosti. Heslo se úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31003 | PasswordResetFail | Tato událost označuje, že uživatel vybral heslo a heslo bylo úspěšně doručeno do místního prostředí. Ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtrování pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například doména nebo podniková skupina pro správu, která nepovoluje operace s nastavením hesla.</li></ul>|
| 31004 | OnboardingEventStart | Tato událost nastane, pokud povolíte zpětný zápis hesla pomocí Azure AD Connect a zahájili jsme registraci vaší organizace na webovou službu zpětného zápisu hesla. |
| 31005 | OnboardingEventSuccess | Tato událost označuje, že proces připojování byl úspěšný a že funkce zpětného zápisu hesla je připravená k použití. |
| 31006 | ChangePasswordStart | Tato událost označuje, že místní služba zjistila požadavek na změnu hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnotu hash hesla, které pocházejí z cloudu. Tato událost představuje první událost při každé operaci zpětného zápisu změny hesla. |
| 31007 | ChangePasswordSuccess | Tato událost indikuje, že uživatel během operace změny hesla vybral nové heslo. zjistili jsme, že heslo splňuje požadavky na heslo společnosti a že se heslo úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31008 | ChangePasswordFail | Tato událost indikuje, že uživatel vybral heslo a že se heslo úspěšně přijalo do místního prostředí, ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtrování pro doménu. Chcete-li tento problém vyřešit, vytvořte nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například Domain nebo Enterprise Admins, což zakazuje operace sady hesel.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Místní služba zjistila požadavek na resetování hesla pro federované, předávací ověřování nebo uživatele synchronizující hodnoty hash hesla, které pocházejí od správce jménem uživatele. Tato událost představuje první událost při každé operaci zpětného zápisu hesla, kterou iniciuje správce. |
| 31010 | ResetUserPasswordByAdminSuccess | Správce vybral nové heslo během operace resetování hesla iniciované správcem. Zjistili jsme, že toto heslo splňuje požadavky na heslo společnosti. Heslo se úspěšně zapsalo zpátky do místního prostředí Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Správce vybral jménem uživatele heslo. Heslo bylo úspěšně doručeno do místního prostředí. Ale při pokusu o nastavení hesla v místním prostředí Active Directory došlo k chybě. K této chybě může dojít z několika důvodů: <br><ul><li>Heslo uživatele nesplňuje požadavky na stáří, historii, složitost nebo filtrování pro doménu. Pokud chcete tento problém vyřešit, zkuste nové heslo.</li><li>Účet služby ADMA nemá příslušná oprávnění k nastavení nového hesla na daném uživatelském účtu.</li><li>Uživatelský účet je v chráněné skupině, jako je například Domain nebo Enterprise Admins, což zakazuje operace sady hesel.</li></ul>  |
| 31012 | OffboardingEventStart | Tato událost nastane, pokud zakážete zpětný zápis hesla pomocí Azure AD Connect a indikujeme, že jsme zahájili zrušení vaší organizace na webovou službu pro zpětný zápis hesla. |
| 31013| OffboardingEventSuccess| Tato událost označuje, že proces zrušení byl úspěšný a že funkce zpětného zápisu hesla byla úspěšně zakázána. |
| 31014| OffboardingEventFail| Tato událost označuje, že proces zrušení nebyl úspěšný. Důvodem může být chyba oprávnění v cloudu nebo místním účtu správce, který jste zadali během konfigurace. K této chybě může dojít také v případě, že se pokoušíte použít globálního správce federovaného cloudu při zákazu zpětného zápisu hesla. Chcete-li tento problém vyřešit, zkontrolujte oprávnění správce a ujistěte se, že při konfiguraci funkce zpětného zápisu hesla nepoužíváte federovaný účet.|
| 31015| WriteBackServiceStarted| Tato událost označuje, že služba zpětného zápisu hesla byla úspěšně spuštěna. Je připraven přijmout požadavky na správu hesel z cloudu.|
| 31016| WriteBackServiceStopped| Tato událost označuje, že služba zpětného zápisu hesla byla zastavena. Všechny požadavky na správu hesel z cloudu nebudou úspěšné.|
| 31017| AuthTokenSuccess| Tato událost označuje, že jsme úspěšně načetli autorizační token pro globálního správce zadaný během Azure AD Connect nastavení pro spuštění procesu odinstalace nebo zprovoznění.|
| 31018| KeyPairCreationSuccess| Tato událost označuje, že jsme úspěšně vytvořili šifrovací klíč hesla. Tento klíč slouží k šifrování hesel z cloudu, které se mají odeslat do místního prostředí.|
| 31034| ServiceBusListenerError| Tato událost označuje, že došlo k chybě při připojování k naslouchacímu procesu Service Bus vašeho tenanta. Pokud chybová zpráva obsahuje "vzdálený certifikát je neplatný", zkontrolujte, zda má server Azure AD Connect všechny požadované kořenové certifikační autority, jak je popsáno v tématu [změny certifikátu Azure TLS](../../security/fundamentals/tls-certificate-changes.md). |
| 32000| UnknownError| Tato událost indikuje, že během operace správy hesel došlo k neznámé chybě. Další podrobnosti najdete v textu výjimky v události. Pokud máte problémy, zkuste zakázat a znovu povolit zpětný zápis hesla. Pokud to nepomůže, zahrňte kopii protokolu událostí spolu s ID sledování zadaným při otevření žádosti o podporu.|
| 32001| ServiceError| Tato událost indikuje, že došlo k chybě při připojování ke službě cloudového resetování hesla. K této chybě obvykle dochází v případě, že se místní služba nemohla připojit k webové službě resetování hesla.|
| 32002| ServiceBusError| Tato událost indikuje, že došlo k chybě při připojování k instanci Service Bus vašeho tenanta. K tomu může dojít, Pokud blokujete odchozí připojení v místním prostředí. Zkontrolujte bránu firewall, abyste měli jistotu, že povolíte připojení přes TCP 443 a na https://ssprdedicatedsbprodncu.servicebus.windows.net , a pak to zkuste znovu. Pokud stále dochází k problémům, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32003| InPutValidationError| Tato událost označuje, že vstup předaný do našeho rozhraní API webové služby byl neplatný. Zkuste operaci zopakovat.|
| 32004| DecryptionError| Tato událost označuje, že došlo k chybě při dešifrování hesla, které bylo doručeno z cloudu. Příčinou může být neshoda dešifrovacího klíče mezi cloudovou službou a místním prostředím. Pokud chcete tento problém vyřešit, zakažte a znovu povolte zpětný zápis hesla v místním prostředí.|
| 32005| ConfigurationError| Během připojování ukládáme informace specifické pro tenanta do konfiguračního souboru ve vašem místním prostředí. Tato událost znamená, že při ukládání tohoto souboru nebo při spuštění služby došlo k chybě. při čtení souboru došlo k chybě. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla, aby se vynutilo přepsání konfiguračního souboru.|
| 32007| OnBoardingConfigUpdateError| Během připojování pošleme data z cloudu do místní služby pro resetování hesla. Tato data se pak zapíší do souboru v paměti předtím, než se odešlou do služby synchronizace, která se uloží bezpečně na disk. Tato událost označuje, že došlo k potížím při zápisu nebo aktualizaci těchto dat v paměti. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla, aby se vynutil přepis tohoto konfiguračního souboru.|
| 32008| ValidationError| Tato událost znamená, že jsme dostali neplatnou odpověď z webové služby pro resetování hesla. Pokud chcete tento problém vyřešit, zkuste zakázat a znovu povolit zpětný zápis hesla.|
| 32009| AuthTokenError| Tato událost označuje, že se nám nepovedlo získat autorizační token pro účet globálního správce zadaný při Azure AD Connect instalaci. Tato chyba může být způsobena nesprávným uživatelským jménem nebo heslem určeným pro účet globálního správce. K této chybě může dojít také v případě, že zadaný účet globálního správce je federovaný. Pokud chcete tento problém vyřešit, spusťte znovu konfiguraci se správným uživatelským jménem a heslem a ujistěte se, že správce je účet spravovaný (jenom cloudový nebo synchronizovaný s heslem).|
| 32010| CryptoError| Tato událost indikuje, že došlo k chybě při generování šifrovacího klíče pro heslo nebo při dešifrování hesla, které přicházejí z cloudové služby. Tato chyba zřejmě indikuje problém s vaším prostředím. Další informace o tom, jak tento problém vyřešit, najdete v podrobnostech v protokolu událostí. Můžete také zkusit zakázat a znovu povolit službu zpětného zápisu hesla.|
| 32011| OnBoardingServiceError| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou resetování hesla, aby zahájila proces připojování. K tomu může dojít v důsledku pravidla brány firewall nebo v případě, že došlo k potížím s získáním ověřovacího tokenu pro vašeho tenanta. Chcete-li tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes protokol TCP 443 a TCP 9350-9354 nebo do https://ssprdedicatedsbprodncu.servicebus.windows.net . Také se ujistěte, že účet správce Azure AD, který používáte k připojování, není federovaný.|
| 32013| OffBoardingError| Tato událost označuje, že místní služba nemohla správně komunikovat s webovou službou resetování hesla, aby zahájila proces odhlašování. K tomu může dojít v důsledku pravidla brány firewall nebo v případě, že došlo k potížím s získáním autorizačního tokenu pro vašeho tenanta. Pokud chcete tento problém vyřešit, ujistěte se, že neblokujete odchozí připojení přes 443 nebo do a https://ssprdedicatedsbprodncu.servicebus.windows.net že účet správce Azure Active Directory, který používáte k odpojení, není federovaný.|
| 32014| ServiceBusWarning| Tato událost označuje, že se musel znovu připojit k instanci Service Bus vašeho tenanta. Za běžných podmínek by to nemělo být obavy, ale pokud se tato událost několikrát zobrazí, zvažte možnost zkontrolovat připojení k síti Service Bus, zejména v případě, že se jedná o připojení s vysokou latencí nebo malou šířkou pásma.|
| 32015| ReportServiceHealthError| Aby bylo možné monitorovat stav služby zpětného zápisu hesel, pošleme data prezenčního signálu do webové služby pro resetování hesla každých pět minut. Tato událost označuje, že při posílání těchto informací o stavu zpět do cloudové webové služby došlo k chybě. Tyto informace o stavu neobsahují žádné osobní údaje a jsou čistě prezenční a základní Statistika služby, aby bylo možné v cloudu poskytovat informace o stavu služby.|
| 33001| ADUnKnownError| Tato událost označuje, že služba Active Directory vrátila neznámou chybu. Další informace najdete v protokolu událostí Azure AD Connect serveru pro události ze zdroje ADSync.|
| 33002| ADUserNotFoundError| Tato událost označuje, že uživatel, který se pokouší resetovat nebo změnit heslo, nebyl nalezen v místním adresáři. K této chybě může dojít, když byl uživatel místně odstraněn, ale ne v cloudu. K této chybě může dojít také v případě, že dojde k potížím se synchronizací. Další informace najdete v protokolech synchronizace a v posledních několika podrobnostech o spuštění synchronizace.|
| 33003| ADMutliMatchError| Když požadavek na resetování hesla nebo žádost o změnu pochází z cloudu, použijeme kotvy cloudu, které jste zadali během procesu instalace Azure AD Connect k určení, jak propojit tento požadavek zpět s uživatelem v místním prostředí. Tato událost označuje, že jsme v místním adresáři našli dva uživatele se stejným atributem kotvy cloudu. Další informace najdete v protokolech synchronizace a v posledních několika podrobnostech o spuštění synchronizace.|
| 33004| ADPermissionsError| Tato událost označuje, že účet služby Agent pro správu služby Active Directory (ADMA) nemá v daném účtu příslušná oprávnění k nastavení nového hesla. Ujistěte se, že účet ADMA v doménové struktuře uživatele má oprávnění k resetování hesla u všech objektů v doménové struktuře. Další informace o tom, jak nastavit oprávnění, najdete v části Krok 4: nastavení příslušných oprávnění služby Active Directory. K této chybě může dojít také v případě, že je atribut uživatele AdminCount nastaven na hodnotu 1.|
| 33005| ADUserAccountDisabled| Tato událost označuje, že došlo k pokusu o resetování nebo změnu hesla k účtu, který byl zablokován místně. Povolte účet a zkuste operaci zopakovat.|
| 33006| ADUserAccountLockedOut| Tato událost označuje, že došlo k pokusu o resetování nebo změnu hesla k účtu, který byl uzamčen místně. K uzamčení může dojít, když uživatel v krátké době vyzkouší příliš mnoho operací změna nebo resetování hesla. Odemkněte účet a zkuste operaci zopakovat.|
| 33007| ADUserIncorrectPassword| Tato událost označuje, že při provádění operace změny hesla zadal uživatel nesprávné aktuální heslo. Zadejte správné aktuální heslo a zkuste to znovu.|
| 33008| ADPasswordPolicyError| K této události dojde, když se služba zpětného zápisu hesla pokusí nastavit heslo v místním adresáři, které nesplňuje stáří hesla, historii, složitost nebo požadavky na filtrování domény. <br> <br> Pokud máte minimální stáří hesla a v časovém intervalu jste nedávno změnili heslo, nebudete moct znovu změnit heslo, dokud nedosáhne zadaného stáří ve vaší doméně. Pro účely testování musí být minimální stáří nastavené na 0. <br> <br> Pokud máte povolené požadavky na historii hesel, musíte vybrat heslo, které se nepoužilo v posledních *n* časech, kde *N* je nastavení historie hesla. Pokud vyberete heslo, které se v posledních *N* časech používalo, zobrazí se v tomto případě chyba. Pro účely testování by měla být historie hesel nastavená na 0. <br> <br> Pokud máte požadavky na složitost hesla, budou všechny tyto zásady vynutily, když se uživatel pokusí změnit nebo resetovat heslo. <br> <br> Pokud máte povolené filtry hesel a uživatel vybere heslo, které nesplňuje kritéria filtrování, operace obnovení nebo změny se nezdařila.|
| 33009| ADConfigurationError| Tato událost indikuje, že došlo k potížím při zápisu hesla zpátky do místního adresáře kvůli potížím s konfigurací se službou Active Directory. Další informace o tom, jaká chyba se stala, najdete v protokolu událostí aplikace v Azure AD Connectovém počítači pro zprávy ze služby ADSync.|

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