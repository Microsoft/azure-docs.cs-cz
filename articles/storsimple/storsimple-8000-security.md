---
title: Zabezpečení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje funkce zabezpečení a ochrany osobních údajů, které chrání vaši službu StorSimple, zařízení a data v místním prostředí a v cloudu.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891503"
---
# <a name="storsimple-security-and-data-protection"></a>StorJednoduché zabezpečení a ochrana dat

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Bezpečnost je velkým problémem pro každého, kdo přijímá novou technologii, zejména pokud je tato technologie používána s důvěrnými nebo proprietárními daty. Při vyhodnocování různých technologií je třeba zvážit zvýšená rizika a náklady na ochranu údajů. Microsoft Azure StorSimple poskytuje řešení zabezpečení i ochrany osobních údajů pro ochranu dat, které pomáhá zajistit:

* **Důvěrnost** – Vaše data mohou zobrazit pouze oprávněné subjekty.
* **Integrita** – Data mohou upravovat nebo odstraňovat pouze oprávněné entity.

Řešení Microsoft Azure StorSimple se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

* **Služba StorSimple Device Manager hostovaná v Microsoft Azure** – služba pro správu, kterou používáte ke konfiguraci a zřízení zařízení StorSimple.
* **StorSimple zařízení** – fyzické zařízení nainstalované ve vašem datovém centru. Všichni hostitelé a klienti, kteří generují data, se připojují k zařízení StorSimple a zařízení je spravuje a podle potřeby je přesune do cloudu Azure.
* **Klienti/hostitelé připojení k zařízení** – klienti ve vaší infrastruktuře, které se připojují k zařízení StorSimple a generují data, která je třeba chránit.
* **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží.

Následující části popisují funkce zabezpečení StorSimple, které pomáhají chránit každou z těchto součástí a data uložená v nich. Obsahuje také seznam otázek, které můžete mít o zabezpečení Microsoft Azure StorSimple a odpovídající odpovědi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrana služby StorSimple Device Manager

Služba StorSimple Device Manager je služba pro správu hostovaná v Microsoft Azure a používaná ke správě všech zařízení StorSimple, která vaše organizace získala. Ke službě StorSimple Device Manager můžete přistupovat pomocí pověření organizace k přihlášení k portálu Azure prostřednictvím webového prohlížeče.

Přístup ke službě StorSimple Device Manager vyžaduje, aby vaše organizace měla předplatné Azure, které zahrnuje StorSimple. Vaše předplatné určuje funkce, které máte na webu Azure Portal dostupné. Pokud vaše organizace nemá předplatné Azure a chcete o nich získat další informace, přečtěte [si přečtěte si název Registrace do Azure jako organizace](../active-directory/fundamentals/sign-up-organization.md).

Vzhledem k tomu, že služba StorSimple Device Manager je hostovaná v Azure, je chráněna funkcemi zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>StorJednoduchá ochrana zařízení

Zařízení StorSimple je místní hybridní paměťové zařízení, které obsahuje disky SSD a pevné disky (HDD) spolu s redundantními řadiči a automatickými možnostmi převzetí služeb při selhání. Řadiče spravují vrstvení úložiště, umísťují aktuálně používaná (nebo horká) data do místního úložiště (v zařízení StorSimple nebo místních serverech) při přesouvání méně často používaných dat do cloudu.

Ke službě StorSimple, kterou jste vytvořili v předplatném Azure, se mohou připojit pouze autorizovaná zařízení StorSimple. Chcete-li autorizovat zařízení, musíte jej zaregistrovat u služby StorSimple Device Manager poskytnutím registračního klíče služby. Registrační klíč služby je 128bitový náhodný klíč generovaný na webu Azure Portal.

![Registrační klíč služby](./media/storsimple-security/ServiceRegistrationKey.png)

Chcete-li se dozvědět, jak získat registrační klíč služby, přejděte ke [kroku 2: Získání registračního klíče služby](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Registrační klíč služby je dlouhý klíč, který obsahuje více než 100 znaků. Klíč můžete zkopírovat a uložit do textového souboru na bezpečném místě, abyste jej mohli podle potřeby použít k autorizaci dalších zařízení. Pokud dojde ke ztrátě registračního klíče služby po registraci prvního zařízení, můžete vygenerovat nový klíč ze služby StorSimple Device Manager. To nebude mít vliv na provoz stávajících zařízení.

Po registraci zařízení používá tokeny ke komunikaci s Microsoft Azure. Registrační klíč služby se nepoužívá po registraci zařízení.

> [!NOTE]
> Doporučujeme znovu vygenerovat registrační klíč služby po každém použití.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Chraňte své řešení StorSimple pomocí hesel

Hesla jsou důležitým aspektem zabezpečení počítače a jsou široce používány v řešení StorSimple, aby bylo zajištěno, že vaše data jsou přístupná pouze oprávněným uživatelům. StorSimple umožňuje konfigurovat následující hesla:

* Heslo správce zařízení StorSimple
* Iniciátor a cílová hesla protokolu CHAP (Challenge Handshake Authentication Protocol)
* Heslo správce Snapshot Manageru zařízení StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Prostředí Windows PowerShell pro storsimple a heslo správce zařízení StorSimple

Prostředí Windows PowerShell for StorSimple je rozhraní příkazového řádku, které můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell for StorSimple obsahuje funkce, které umožňují zaregistrovat zařízení, konfigurovat síťové rozhraní v zařízení, instalovat určité typy aktualizací, řešit potíže se zařízením přístupem k relaci podpory a měnit stav zařízení. K prostředí Windows PowerShell pro Službu StorSimple můžete přistupovat připojením k sériové konzoli v zařízení nebo pomocí vzdálené komunikace prostředí Windows PowerShell.

Vzdálené ověřování prostředí PowerShellu lze provést přes protokol HTTPS nebo HTTP. Pokud je povolena vzdálená správa prostředpou HTTPS, budete muset stáhnout certifikát vzdálené správy ze zařízení a nainstalovat jej do vzdáleného klienta. Další informace o vzdálené modusle prostředí PowerShell najdete v části [Vzdálená připojení k zařízení StorSimple](storsimple-8000-remote-connect.md).

Po použití prostředí Windows PowerShell pro StorSimple pro připojení k zařízení, budete muset zadat heslo správce zařízení pro přihlášení k zařízení.

![Heslo správce zařízení](./media/storsimple-security/DeviceAdminPW.png)

Mějte na paměti následující doporučené postupy:

* Vzdálená správa je ve výchozím nastavení vypnuta. K povolení můžete použít službu Správce zařízení StorSimple. Z bezpečnostních důvodů by měl být vzdálený přístup povolen pouze během období, které je skutečně zapotřebí.
* Pokud změníte heslo, nezapomeňte upozornit všechny uživatele vzdáleného přístupu, aby nedošlo k neočekávané ztrátě připojení.
* Služba StorSimple Device Manager nemůže načíst existující hesla: může je pouze obnovit. Doporučujeme uložit všechna hesla na bezpečném místě, abyste nemuseli resetovat heslo, pokud je zapomenuto. Pokud potřebujete obnovit heslo, nezapomeňte před resetováním upozornit všechny uživatele.

K rozhraní prostředí Windows PowerShell můžete přistupovat pomocí sériového připojení k zařízení. Můžete k němu také vzdáleně přistupovat pomocí protokolu HTTP nebo HTTPS, které poskytují další zabezpečení. Protokol HTTPS poskytuje vyšší úroveň zabezpečení než sériové připojení nebo připojení HTTP. Chcete-li však používat protokol HTTPS, musíte nejprve nainstalovat certifikát do klientského počítače, který bude přistupovat k zařízení. Certifikát vzdáleného přístupu si můžete stáhnout ze stránky konfigurace zařízení ve službě StorSimple Device Manager. Pokud dojde ke ztrátě certifikátu pro vzdálený přístup, je nutné stáhnout nový certifikát a šířit jej všem klientům, kteří jsou oprávněni používat vzdálenou správu.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Iniciátor a cílová hesla protokolu CHAP (Challenge Handshake Authentication Protocol)

Chap je schéma ověřování používané zařízením StorSimple k ověření identity vzdálených klientů. Ověření je založeno na sdíleném hesle. Chap může být jednosměrný (jednosměrný) nebo vzájemný (obousměrný). S jednosměrný CHAP cíl (StorSimple zařízení) ověřuje iniciátor (hostitel). Vzájemné nebo reverzní CHAP vyžaduje, aby cíl ověřit iniciátor a potom iniciátor ověřit cíl. Vaše StorSimple lze nakonfigurovat tak, aby používala obě metody.

Při konfiguraci protokolu CHAP si uvědomte následující:

* Uživatelské jméno chap musí obsahovat méně než 233 znaků.
* Heslo chap musí být mezi 12 a 16 znaky. Pokus o použití delšího uživatelského jména nebo hesla bude mít za následek selhání ověřování na hostiteli systému Windows.
* Stejné heslo nelze použít pro iniciátor chap i cíl CHAP.
* Po nastavení hesla jej lze změnit, ale nelze jej načíst. Pokud se změní heslo, nezapomeňte upozornit všechny uživatele vzdáleného přístupu, aby se mohli úspěšně připojit k zařízení StorSimple.

Další informace o protokolu CHAP a jeho konfiguraci pro řešení StorSimple naleznete v [části Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Heslo správce Snapshot Manageru zařízení StorSimple

StorSimple Snapshot Manager je modul snap-in konzoly MMC (MMC), který ke generování záloh konzistentních s aplikací používá skupiny svazků a službu Stínové kopie svazků systému Windows. Kromě toho můžete použít Správce snímků StorSimple k vytvoření plánů zálohování a klonování nebo obnovení svazků.

Při konfiguraci zařízení pro použití Správce snímků StorSimple, budete muset zadat heslo Správce snímků StorSimple. Toto heslo je nejprve nastaveno v prostředí Windows PowerShell pro StorSimple během registrace. Heslo lze také nastavit a změnit ze služby StorSimple Device Manager. Toto heslo ověřuje zařízení pomocí Správce snímků StorSimple.

![Heslo správce Snapshot Manageru zařízení StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

Heslo správce snímků StorSimple musí mít 14 až 15 znaků a musí obsahovat 3 nebo více kombinací velkých, malých, číselných a speciálních znaků. Po nastavení hesla Správce snímků StorSimple jej lze změnit, ale nelze jej načíst. Pokud změníte heslo, nezapomeňte upozornit všechny vzdálené uživatele.

Další informace o Správci snímků StorSimple naleznete v najdete v informacích [O tom, co je Správce snímků StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Osvědčené postupy pro hesla

Doporučujeme použít následující pokyny, které vám pomohou zajistit, aby hesla StorSimple byla silná a dobře chráněná:

* Změňte hesla každé tři měsíce. Změna hesla je vynucena každoročně.
* Používejte silná hesla. Další informace naleznete v [tématu Vytvoření silnějších hesel a jejich ochrana](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Vždy používejte různá hesla pro různé mechanismy přístupu; každé zadané heslo by mělo být jedinečné.
* Nesdílejte hesla s nikým, kdo nemá oprávnění k přístupu k zařízení StorSimple.
* Nemluvte o hesle před ostatními ani nenaznačujte formát hesla.
* Pokud máte podezření, že byl poškozen účet nebo heslo, nahlaste incident svému oddělení pro bezpečnost informací.
* Zacházejte se všemi hesly jako s citlivými důvěrnými informacemi. 

## <a name="storsimple-data-protection"></a>StorJednoduchá ochrana dat

Tato část popisuje funkce zabezpečení StorSimple, které chrání data při přenosu a uložená data.

Jak je popsáno v jiných částech, hesla se používají k autorizaci a ověření uživatelů před tím, než mohou získat přístup k řešení StorSimple. Dalším aspektem zabezpečení je ochrana dat před neoprávněnými uživateli při jejich přenosu mezi systémy úložiště a při jejich ukládání. Následující části popisují funkce ochrany dat poskytované s StorSimple.

> [!NOTE]
> Odstranění duplicit poskytuje další ochranu dat uložených na zařízení StorSimple a v úložišti Microsoft Azure. Při odstranění duplicit dat jsou datové objekty uloženy odděleně od metadat použitých k mapování a přístupu k nim: neexistuje žádný dostupný kontext na úrovni úložiště, který by rekonstruoval data na základě struktury svazku, systému souborů nebo názvu souboru.


## <a name="protect-data-flowing-through-the-service"></a>Ochrana dat protékajících službou

Primárním účelem služby StorSimple Device Manager je správa a konfigurace zařízení StorSimple. Služba StorSimple Device Manager běží v Microsoft Azure. Na webu Azure Portal můžete zadávat konfigurační data zařízení a pak Microsoft Azure použije službu StorSimple Device Manager k odeslání dat do zařízení. StorSimple používá systém asymetrických párů klíčů k zajištění, že ohrožení zabezpečení služby Azure nebude mít za následek ohrožení zabezpečení uložených informací.

![Šifrování dat za letu](./media/storsimple-security/DataEncryption.png)

Asymetrický systém klíčů pomáhá chránit data, která protéká službou, následujícím způsobem:

1. V zařízení je generován certifikát pro šifrování dat, který používá asymetrický pár veřejného a soukromého klíče, který se používá k ochraně dat. Klíče jsou generovány při registraci prvního zařízení.
2. Klíče certifikátu šifrování dat jsou exportovány do souboru Výměny osobních informací (.pfx), který je chráněn šifrovacím klíčem služby, což je silný 128bitový klíč, který je náhodně generován prvním zařízením během registrace.
3. Veřejný klíč certifikátu je bezpečně k dispozici službě StorSimple Device Manager a soukromý klíč zůstane se zařízením.
4. Data vstupující do služby se zašifrují pomocí veřejného klíče a dešifrují pomocí privátního klíče uloženého v zařízení, což zajišťuje, že služba Azure nemůže dešifrovat data toku do zařízení.

Šifrovací klíč dat služby je generován pouze na prvním zařízení registrovaném ve službě. Všechna následující zařízení, která jsou registrována ve službě, musí používat stejný šifrovací klíč dat služby.

> [!IMPORTANT]
> Je velmi důležité vytvořit kopii šifrovacího klíče dat služby a uložit jej na bezpečném místě. Kopie šifrovacího klíče dat služby by měla být uložena tak, aby k ní měla přístup oprávněná osoba a aby ji mohla snadno sdělit správce zařízení.
> 
> Pokud dojde ke ztrátě šifrovacího klíče dat služby, může vám osoba podpory společnosti Microsoft pomoci s jeho načtením za předpokladu, že máte alespoň jedno zařízení ve stavu online. Doporučujeme změnit šifrovací klíč dat služby po jeho načtení.

Chcete-li změnit šifrovací klíč dat služby a odpovídající certifikát šifrování dat, postupujte podle pokynů v [tématu Změna šifrovacího klíče dat služby pro službu StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Změna šifrovacích klíčů vyžaduje, aby byla všechna zařízení aktualizována novým klíčem. Proto doporučujeme změnit klíč, když jsou všechna zařízení online. Pokud jsou zařízení offline, jejich klíče lze změnit v jiném čase. Zařízení s zastaralými klíči budou stále moci spouštět zálohy, ale nebudou moci obnovit data, dokud nebude klíč aktualizován.

Platnost šifrovacího klíče dat služby a certifikátu šifrování dat nevyprší. Doporučujeme však změnit kód pro šifrování dat služby každý rok, aby se zabránilo ohrožení zabezpečení klíčů.

## <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

Zařízení StorSimple spravuje data tak, že je ukládá místně a v cloudu v závislosti na četnosti použití. Všechny hostitelské počítače, které jsou připojeny k zařízení, odesílají data do zařízení, které pak podle potřeby přesune data do cloudu. Data se přenášejí ze zařízení do cloudu bezpečně přes internet. Každé zařízení má jeden cíl iSCSI, který zobrazuje všechny sdílené svazky na tomto zařízení. Všechna data jsou před odesláním do cloudového úložiště zašifrována. 

![Šifrovací klíč cloudového úložiště](./media/storsimple-security/CloudStorageEncryption.png)

Chcete-li zajistit zabezpečení a integritu dat přesunutých do cloudu, StorSimple umožňuje definovat šifrovací klíče cloudového úložiště následujícím způsobem:

* Šifrovací klíč cloudového úložiště zadáte při vytváření kontejneru svazku. Klíč nelze později změnit ani přidat.
* Všechny svazky v kontejneru svazků sdílejí stejný šifrovací klíč. Pokud chcete pro určitý svazek použít jinou formu šifrování, doporučujeme vytvořit nový kontejner svazku pro hostování tohoto svazku.
* Když zadáte šifrovací klíč cloudového úložiště ve službě StorSimple Device Manager, klíč je šifrován pomocí veřejné části šifrovacího klíče dat služby a poté odeslán do zařízení.
* Šifrovací klíč cloudového úložiště není uložen nikde ve službě a je znám pouze zařízení.
* Určení šifrovacího klíče cloudového úložiště je volitelné. Do zařízení můžete odeslat data, která byla na hostiteli zašifrována.

### <a name="additional-security-best-practices"></a>Další doporučené postupy zabezpečení

* Rozdělený provoz: Izolujte síť iSCSI SAN od provozu uživatelů v podnikové síti LAN nasazením zcela oddělené sítě a použitím sítí VLAN, kde fyzická izolace nepřichází v úvahu. Vyhrazená síť pro úložiště iSCSI zaručí bezpečnost a výkon důležitých podnikových dat. Míchání úložiště a uživatelského provozu přes podnikovou síť LAN se nedoporučuje a může zvýšit latenci a způsobit selhání sítě.
* Pro zabezpečení sítě na straně hostitele použijte síťová rozhraní, která podporují modul ToE (TCP/IP Offload Engine). TOE snižuje zatížení procesoru zpracováním protokolu TCP na síťovém adaptéru.

## <a name="protect-data-via-storage-accounts"></a>Ochrana dat prostřednictvím účtů úložiště

Každé předplatné Microsoft Azure může vytvořit jeden nebo více účtů úložiště. (Účet úložiště poskytuje jedinečný obor názvů pro práci s daty uloženými v cloudu Azure.) Přístup k účtu úložiště je řízen předplatným a přístupovými klíči přidruženými k tomuto účtu úložiště.

Když vytvoříte účet úložiště, Microsoft Azure vygeneruje dva 512bitové přístupové klíče úložiště, z nichž jeden se používá k ověřování, když zařízení StorSimple přistupuje k účtu úložiště. Všimněte si, že pouze jeden z těchto klíčů je používán. Druhý klíč je držen v záloze, což vám umožní pravidelně otáčet klávesy. Chcete-li otočit klávesy, vytvořte aktivní sekundární klíč a odstraňte primární klíč. Poté můžete vytvořit nový klíč pro použití během dalšího otočení. (Z bezpečnostních důvodů vyžaduje mnoho datových center střídání klíčů.)

Doporučujeme dodržovat tyto doporučené postupy pro střídání klíčů:

* Klíče účtu úložiště byste měli pravidelně střídat, abyste zajistili, že k vašemu účtu úložiště nebudou mít přístup neoprávnění uživatelé.
* Správce Azure by měl pravidelně měnit nebo regenerovat primární nebo sekundární klíč pomocí části Úložiště na webu Azure Portal pro přímý přístup k účtu úložiště.

## <a name="protect-data-via-encryption"></a>Ochrana dat pomocí šifrování

StorSimple používá následující šifrovací algoritmy k ochraně dat uložených v nebo cestování mezi součástmi vašeho řešení StorSimple.

| Algoritmus | Délka klíče | Protokoly/aplikace/komentáře |
| --- | --- | --- |
| RSA |2 048 |RSA PKCS 1 v1.5 používá portál Azure k šifrování konfiguračních dat, která se odesílají do zařízení: například pověření účtu úložiště, konfigurace zařízení StorSimple a šifrovací klíče cloudového úložiště. |
| AES |256 |AES s CBC se používá k šifrování veřejné části šifrovacího klíče dat služby před odesláním na portál Azure ze zařízení StorSimple. Zařízení StorSimple jej také používá k šifrování dat před odesláním dat do účtu cloudového úložiště. |

## <a name="storsimple-cloud-appliance-security"></a>Zabezpečení cloudových zařízení StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Správa osobních údajů

Správce zařízení StorSimple pro fyzické i virtuální řady shromažďuje osobní údaje v následujících klíčových případech:

- Upozornit uživatele na nastavení, kde je nakonfigurována e-mailová adresa uživatelů. Tyto informace mohou být zobrazeny a vymazány správcem. To platí jak pro zařízení řady StorSimple 8000, tak pro virtuální pole StorSimple.
  * Chcete-li zobrazit a vymazat nastavení řady StorSimple 8000, postupujte podle pokynů v [části Zobrazení a správa výstrah StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Chcete-li zobrazit a vymazat nastavení virtuálního pole StorSimple, postupujte podle pokynů v [části Zobrazení a správa výstrah StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Uživatelé, kteří mají přístup k datům s bydlištěm ve sdílených složek. Zobrazí se seznam uživatelů, kteří mají přístup k datům sdílené složky, a lze jej zobrazit. Tento seznam je také odstraněn při odstranění sdílených složek. To platí pouze pro virtuální pole StorSimple.
  * Chcete-li zobrazit seznam uživatelů, kteří mají přístup ke sdílené položce nebo ji odstranit, postupujte podle pokynů v části [Správa sdílených složek ve virtuálním poli StorSimple](storsimple-virtual-array-manage-shares.md)

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Následují některé otázky a odpovědi týkající se zabezpečení a Microsoft Azure StorSimple.

**Otázka:** Moje služba je ohrožena. Jaké by měly být mé další kroky?

**A:** Měli byste okamžitě změnit šifrovací klíč dat služby a klíče účtu úložiště pro účet úložiště, který se používá pro vrstvení dat. Pokyny naleznete na adrese:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Střídání klíčů účtů úložiště](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Otázka:** Mám nové zařízení StorSimple, které žádá o registrační klíč služby. Jak ji získám?

**A:** Tento klíč byl vytvořen při prvním vytvoření služby StorSimple Device Manager. Při použití služby StorSimple Device Manager pro připojení k zařízení, můžete použít stránku rychlého spuštění služby k zobrazení nebo obnovení registračního klíče služby. Generování nového registračního klíče služby neovlivní existující registrovaná zařízení. Pokyny naleznete na adrese:

* [Zobrazení nebo obnovení registračního klíče služby](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**Otázka:** Ztratil jsem šifrovací klíč pro data služby. Co mám udělat?

**A:** Obraťte se na podporu společnosti Microsoft. Mohou se přihlásit k relaci podpory na vašem zařízení a pomoci vám načíst klíč (za předpokladu, že alespoň jedno zařízení je online). Ihned po získání šifrovacího klíče dat služby byste jej měli změnit, abyste zajistili, že nový klíč bude znám pouze vám. Pokyny naleznete na adrese:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Otázka:**  Autorizoval jsem zařízení pro změnu šifrovacího klíče dat služby, ale proces změny klíče nebyl zahájen. Co bych měl/a dělat?

**A:** Pokud vypršelčasový limit, budete muset znovu autorizovat zařízení pro změnu šifrovacího klíče dat služby a znovu spustit proces.

**Otázka:**  Změnil jsem šifrovací klíč dat služby, ale nebyl jsem schopen aktualizovat ostatní zařízení do 4 hodin. Musím začít znovu?

**A:** 4hodinové časové období je pouze pro zahájení změny. Po spuštění procesu aktualizace na autorizovaném zařízení StorSimple je autorizace platná, dokud nebudou aktualizována všechna zařízení.

**Otázka:** Náš správce StorSimple opustil společnost. Co bych měl/a dělat?

**A:** Změňte a resetujte hesla, která umožňují přístup k zařízení StorSimple, a změňte šifrovací klíč dat služby, abyste zajistili, že nové informace nebudou známy neoprávněným osobám. Pokyny naleznete na adrese:

* [Změna jednoduchých hesel storsimple správce zařízení pomocí služby StorSimple Device Manager](storsimple-8000-change-passwords.md)
* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md)

**Otázka:** Chci poskytnout heslo Správce snímků StorSimple hostiteli, který se připojuje k zařízení StorSimple, ale heslo není k dispozici. Co mám udělat?

**A:** Pokud jste zapomněli heslo, měli byste vytvořit nové. Potom nezapomeňte informovat všechny stávající uživatele, že heslo bylo změněno a že by měli aktualizovat své klienty, aby používali nové heslo. Pokyny naleznete na adrese:

* [Změna hesla Správce snímků StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Ověření zařízení](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Otázka:** Certifikát pro vzdálený přístup k prostředí Windows PowerShell for StorSimple byl v zařízení změněn. Jak lze aktualizovat klienty vzdáleného přístupu?

**A:** Nový certifikát si můžete stáhnout ze služby StorSimple Device Manager a poté jej poskytnout k instalaci do úložiště certifikátů vzdálených klientů. Pokyny naleznete na adrese:

* [Rutina importu certifikátu](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**Otázka:** Jsou moje data chráněna, pokud je ohrožena služba StorSimple Device Manager?

**A:** Při zobrazení ve webovém prohlížeči jsou data konfigurace služby vždy šifrována pomocí veřejného klíče. Vzhledem k tomu, že služba nemá přístup k soukromému klíči, nebude služba moci zobrazit žádná data. Pokud je ohrožena služba StorSimple Device Manager, neexistuje žádný dopad, protože ve službě StorSimple Device Manager nejsou uloženy žádné klíče.

**Otázka:** Pokud někdo získá přístup k certifikátu šifrování dat, budou moje data ohrožena?

**A:** Microsoft Azure ukládá šifrovací klíč dat zákazníka (soubor.pfx) v šifrovaném formátu. Vzhledem k tomu, že soubor .pfx je zašifrován a služba StorSimple nemá šifrovací klíč služby pro dešifrování souboru Pfx, jednoduše získání přístupu k souboru .pfx neodhalí žádné tajné klíče.

**Otázka:** Co se stane, když se vládní subjekt zeptá microsoftu na moje data?

**A:** Vzhledem k tomu, že všechna data jsou šifrována ve službě a soukromý klíč je uložen se zařízením, musí vládní subjekt požádat zákazníka o data.



## <a name="next-steps"></a>Další kroky

[Nasaďte zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

