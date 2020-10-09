---
title: Zabezpečení řady StorSimple 8000 | Microsoft Docs
description: Popisuje funkce zabezpečení a ochrany osobních údajů, které chrání službu StorSimple, zařízení a data místně a v cloudu.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75891503"
---
# <a name="storsimple-security-and-data-protection"></a>Zabezpečení a ochrana dat v StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled

Zabezpečení je zásadním problémem pro kohokoli, kdo přijímají novou technologii, zejména v případě, že se technologie používá s důvěrnými nebo proprietárními daty. Když vyhodnocujete různé technologie, je třeba zvážit zvýšená rizika a náklady na ochranu dat. Microsoft Azure StorSimple poskytuje řešení zabezpečení a ochrany osobních údajů pro ochranu dat, což pomáhá zajistit:

* **Důvěrné** – data můžou zobrazit jenom autorizovaní entity.
* **Integrity** – data můžou upravovat nebo odstraňovat jenom autorizované entity.

Microsoft Azure StorSimple řešení se skládá ze čtyř hlavních součástí, které vzájemně spolupracují:

* **Služba StorSimple Device Manager hostovaná v Microsoft Azure** – služba správy, kterou můžete použít ke konfiguraci a zřízení zařízení StorSimple.
* **Zařízení StorSimple** – fyzické zařízení nainstalované ve vašem datacentru. Všichni hostitelé a klienti, kteří generují data, se připojují k zařízení StorSimple a zařízení data spravují a v případě potřeby ho přenáší do cloudu Azure.
* **Klienti/hostitelé připojení k zařízení** – klienti v infrastruktuře, kteří se připojují k zařízení StorSimple a generují data, která je třeba chránit.
* **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží.

Následující části popisují funkce zabezpečení StorSimple, které vám pomůžou chránit každou z těchto součástí a data, která jsou na nich uložená. Obsahuje také seznam otázek, které mohou mít Microsoft Azure StorSimple zabezpečení a příslušné odpovědi.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Device Manager Service Protection

Služba StorSimple Device Manager je služba správy hostovaná v Microsoft Azure a používá se ke správě všech zařízení StorSimple, která vaše organizace získala. Ke službě StorSimple Device Manager můžete přistupovat pomocí přihlašovacích údajů vaší organizace, abyste se přihlásili k Azure Portal prostřednictvím webového prohlížeče.

Přístup ke službě StorSimple Device Manager vyžaduje, aby vaše organizace měla předplatné Azure, které zahrnuje StorSimple. Vaše předplatné určuje funkce, které máte na webu Azure Portal dostupné. Pokud vaše organizace nemá předplatné Azure a chcete o nich získat další informace, přečtěte si téma [Registrace do Azure jako organizace](../active-directory/fundamentals/sign-up-organization.md).

Vzhledem k tomu, že je služba StorSimple Device Manager hostovaná v Azure, je chráněná funkcemi zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrana zařízení StorSimple

Zařízení StorSimple je místní hybridní paměťové zařízení, které obsahuje jednotky SSD (Solid State Drive) a jednotky pevného disku (HDD), spolu s redundantními řadiči a možnostmi automatického převzetí služeb při selhání. Řadiče spravují vrstvení úložiště, umísťují aktuálně používaná (nebo horká) data do místního úložiště (v zařízení StorSimple nebo místních serverech) a při přesunu méně často využívaných dat do cloudu.

Ke službě StorSimple Device Manager, kterou jste vytvořili ve svém předplatném Azure, se smí připojit jenom oprávnění zařízení StorSimple. K autorizaci zařízení je nutné ho zaregistrovat ve službě StorSimple Device Manager pomocí registračního klíče služby. Registrační klíč služby je 128 bitový náhodný klíč generovaný v Azure Portal.

![Registrační klíč služby](./media/storsimple-security/ServiceRegistrationKey.png)

Další informace o tom, jak získat registrační klíč služby, najdete [v kroku 2: získání registračního klíče služby](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Registrační klíč služby je dlouhý klíč, který obsahuje více než 100 znaků. Klíč můžete zkopírovat a uložit v textovém souboru na bezpečném místě, abyste ho mohli použít k autorizaci dalších zařízení podle potřeby. Pokud po registraci prvního zařízení dojde ke ztrátě registračního klíče služby, můžete vygenerovat nový klíč ze služby StorSimple Device Manager. Tato akce nebude mít vliv na provoz stávajících zařízení.

Po zaregistrování zařízení používá tokeny ke komunikaci s Microsoft Azure. Registrační klíč služby se nepoužívá po registraci zařízení.

> [!NOTE]
> Po každém použití doporučujeme znovu vygenerovat registrační klíč služby.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Chraňte své řešení StorSimple pomocí hesel

Hesla jsou důležitým aspektem zabezpečení počítače a často se používají v řešení StorSimple, které vám pomůžou zajistit, aby vaše data byla dostupná jenom autorizovaným uživatelům. StorSimple umožňuje nakonfigurovat následující hesla:

* Heslo správce zařízení StorSimple
* Iniciátor protokolu CHAP (Challenge Handshake Authentication Protocol) a cílová hesla
* Heslo správce Snapshot Manageru zařízení StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell pro StorSimple a heslo správce zařízení StorSimple

Windows PowerShell pro StorSimple je rozhraní příkazového řádku, které můžete použít ke správě zařízení StorSimple. Windows PowerShell pro StorSimple mají funkce, které vám umožní zaregistrovat zařízení, nakonfigurovat síťové rozhraní na zařízení, nainstalovat určité typy aktualizací, vyřešit potíže s vaším zařízením a získat přístup k relaci podpory a změnit stav zařízení. Přístup k Windows PowerShell pro StorSimple získáte tak, že se připojíte ke konzole sériového portu na zařízení nebo pomocí vzdálené komunikace Windows PowerShellu.

Vzdálená komunikace PowerShellu se dá provést přes protokol HTTPS nebo HTTP. Pokud je povolená Vzdálená správa přes protokol HTTPS, budete si muset ze zařízení stáhnout certifikát pro vzdálenou správu a nainstalovat ho do vzdáleného klienta. Další informace o vzdálené komunikaci PowerShellu najdete v pro [vzdálené připojení k zařízení StorSimple](storsimple-8000-remote-connect.md).

Po použití Windows PowerShell pro StorSimple k připojení k zařízení budete muset zadat heslo správce zařízení, abyste se mohli přihlásit k zařízení.

![Heslo správce zařízení](./media/storsimple-security/DeviceAdminPW.png)

Mějte na paměti následující osvědčené postupy:

* Vzdálená správa je ve výchozím nastavení vypnutá. K povolení této služby můžete použít službu StorSimple Device Manager. Z hlediska zabezpečení by měl být vzdálený přístup povolen pouze během časového období, které je skutečně potřeba.
* Pokud změníte heslo, nezapomeňte se informovat o všech uživatelích vzdáleného přístupu tak, aby nedošlo k neočekávané ztrátě připojení.
* Služba StorSimple Device Manager nemůže načíst stávající hesla: může je obnovit pouze. Doporučujeme uložit všechna hesla na bezpečném místě, aby při zapomenutí hesla nemuseli resetovat heslo. Pokud budete muset resetovat heslo, nezapomeňte se před resetováním všech uživatelů seznámit.

K rozhraní Windows PowerShell můžete přistupovat pomocí sériového připojení k zařízení. K němu můžete také přistupovat vzdáleně pomocí protokolu HTTP nebo HTTPS, který poskytuje dodatečné zabezpečení. Protokol HTTPS poskytuje vyšší úroveň zabezpečení než sériové připojení nebo připojení HTTP. Pokud ale chcete použít protokol HTTPS, musíte nejdřív nainstalovat certifikát do klientského počítače, který bude mít přístup k zařízení. Certifikát vzdáleného přístupu si můžete stáhnout ze stránky konfigurace zařízení ve službě StorSimple Device Manager. Pokud dojde ke ztrátě certifikátu pro vzdálený přístup, musíte si stáhnout nový certifikát a rozšířit ho na všechny klienty, kteří mají autorizaci používat vzdálenou správu.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Iniciátor protokolu CHAP (Challenge Handshake Authentication Protocol) a cílová hesla

Protokol CHAP je schéma ověřování používané zařízením StorSimple k ověření identity vzdálených klientů. Ověřování je založeno na sdíleném hesle. Protokol CHAP může být jednosměrný (jednosměrný) nebo společný (obousměrný). V případě jednosměrného protokolu CHAP cíl (zařízení StorSimple) ověřuje iniciátor (hostitele). Protokol vzájemného nebo zpětného ověřování CHAP vyžaduje, aby cíl ověřil iniciátora a potom iniciátor ověřil cíl. Vaše StorSimple je možné nakonfigurovat tak, aby používaly kteroukoli z těchto metod.

Při konfiguraci protokolu CHAP mějte na paměti následující:

* Uživatelské jméno protokolu CHAP musí obsahovat méně než 233 znaků.
* Heslo protokolu CHAP musí mít délku 12 až 16 znaků. Pokus o použití delšího uživatelského jména nebo hesla způsobí selhání ověřování na hostiteli Windows.
* Pro iniciátor protokolu CHAP i cíl protokolu CHAP nelze použít stejné heslo.
* Po nastavení můžete heslo změnit, ale nelze ho načíst. Pokud se změní heslo, nezapomeňte se informovat o všech uživatelích vzdáleného přístupu, aby se mohli úspěšně připojit k zařízení StorSimple.

Další informace o protokolu CHAP a o tom, jak ho nakonfigurovat pro řešení StorSimple, najdete v tématu [Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Heslo správce Snapshot Manageru zařízení StorSimple

StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který používá skupiny svazků a služba Stínová kopie svazku Windows ke generování záloh konzistentních vzhledem k aplikacím. Kromě toho můžete k vytváření plánů zálohování a klonování a obnovování svazků použít Snapshot Manager StorSimple.

Když nakonfigurujete zařízení tak, aby používalo Snapshot Manager StorSimple, budete muset zadat heslo StorSimple Snapshot Manager. Toto heslo je nejprve nastaveno v Windows PowerShell pro StorSimple během registrace. Heslo může být také nastaveno a změněno ze služby StorSimple Device Manager. Toto heslo ověřuje zařízení pomocí Snapshot Manager StorSimple.

![Heslo správce Snapshot Manageru zařízení StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

Heslo Snapshot Manager StorSimple musí mít 14 až 15 znaků a musí obsahovat 3 nebo více kombinací velkých a malých písmen, číslic a speciálních znaků. Jakmile nastavíte StorSimple Snapshot Manager heslo, dá se změnit, ale nedá se načíst. Pokud změníte heslo, nezapomeňte se informovat o všech vzdálených uživatelích.

Další informace o StorSimple Snapshot Manager najdete v Snapshot Manager k tomu, [co je StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Osvědčené postupy pro hesla

Doporučujeme, abyste používali následující pokyny, abyste zajistili, že hesla StorSimple jsou silná a dobře chráněná:

* Hesla si změníte každé tři měsíce. Změna hesel se vynutila jednou ročně.
* Používejte silná hesla. Další informace najdete v, kde můžete [vytvářet silnější hesla a chránit je](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Pro různé mechanismy přístupu vždycky používejte jiná hesla; každé heslo, které zadáte, by mělo být jedinečné.
* Nesdílejte hesla se všemi uživateli, kteří nemají oprávnění pro přístup k zařízení StorSimple.
* Nemluvujte o heslech před ostatními nebo pomocným heslem ve formátu hesla.
* Pokud máte podezření, že došlo k ohrožení bezpečnosti účtu nebo hesla, ohlaste incident vašemu oddělení zabezpečení informací.
* Považovat všechna hesla za citlivá a důvěrná informace. 

## <a name="storsimple-data-protection"></a>Ochrana dat StorSimple

Tato část popisuje funkce zabezpečení StorSimple, které chrání data při přenosu a uložená data.

Jak je popsáno v dalších částech, hesla slouží k autorizaci a ověření uživatelů před tím, než získají přístup k řešení StorSimple. Dalším aspektem zabezpečení je ochrana dat před neoprávněnými uživateli během přenosu mezi systémy úložiště a při jejich ukládání. V následujících částech najdete popis funkcí ochrany dat poskytovaných pomocí StorSimple.

> [!NOTE]
> Odstranění duplicitních dat poskytuje dodatečnou ochranu pro data uložená na zařízení StorSimple a ve službě Microsoft Azure Storage. Při odstranění duplicitních dat se datové objekty ukládají odděleně od metadat používaných k namapování a přístup k nim: neexistuje žádný dostupný kontext na úrovni úložiště k rekonstrukci dat na základě struktury svazků, systému souborů nebo názvu souboru.


## <a name="protect-data-flowing-through-the-service"></a>Ochrana dat toku prostřednictvím služby

Hlavním účelem služby StorSimple Device Manager je Správa a konfigurace zařízení StorSimple. Služba StorSimple Device Manager běží v Microsoft Azure. Pomocí Azure Portal zadáte data konfigurace zařízení a pak Microsoft Azure pomocí služby StorSimple Device Manager odesílá data do zařízení. StorSimple používá systém párů asymetrických klíčů, aby bylo možné zajistit, že ohrožení služby Azure nebude mít za následek ohrožení uložených informací.

![Šifrování dat v letu](./media/storsimple-security/DataEncryption.png)

Asymetrický klíčový systém pomáhá chránit data, která se prostřednictvím služby přecházejí následujícím způsobem:

1. Certifikát pro šifrování dat, který používá asymetrický veřejný a privátní klíč, se vygeneruje v zařízení a používá se k ochraně dat. Klíče jsou generovány při registraci prvního zařízení.
2. Klíče certifikátu pro šifrování dat se exportují do souboru Personal Information Exchange (. pfx), který je chráněný šifrovacím klíčem dat služby, což je silný 128 bitový klíč, který je při registraci náhodně generovaný prvním zařízením.
3. Veřejný klíč certifikátu je bezpečně zpřístupněn službě StorSimple Device Manager a privátní klíč zůstane se zařízením.
4. Data, která vstupují do služby, se šifrují pomocí veřejného klíče a dešifrují se pomocí privátního klíče uloženého v zařízení a zajišťují, že služba Azure nemůže dešifrovat tok dat do zařízení.

Šifrovací klíč dat služby se vygeneruje jenom pro první zařízení zaregistrované ve službě. Všechna následující zařízení, která jsou zaregistrovaná ve službě, musí používat stejný šifrovací klíč dat služby.

> [!IMPORTANT]
> Je velmi důležité vytvořit kopii šifrovacího klíče dat služby a uložit ho na bezpečném místě. Kopie šifrovacího klíče dat služby by měla být uložena takovým způsobem, že k ní má oprávnění oprávněná osoba a může být snadno sdělena Správci zařízení.
> 
> Pokud dojde ke ztrátě šifrovacího klíče dat služby, může vám pracovník podpory Microsoftu pomoci ho načíst, pokud máte minimálně jedno zařízení ve stavu online. Po načtení doporučujeme změnit šifrovací klíč dat služby.

Chcete-li změnit šifrovací klíč dat služby a odpovídající certifikát pro šifrování dat, postupujte podle kroků v části [Změna šifrovacího klíče dat služby pro službu StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Změna šifrovacích klíčů vyžaduje, aby byla všechna zařízení aktualizována pomocí nového klíče. Proto doporučujeme, abyste změnili klíč, když jsou všechna zařízení online. Pokud jsou zařízení offline, můžou se jejich klíče změnit v jinou dobu. Zařízení se zastaralými klíči budou moci spustit zálohování, ale nebudou moci data obnovit, dokud nebude klíč aktualizován.

Platnost šifrovacího klíče dat služby a certifikátu pro šifrování dat nevyprší. Nicméně doporučujeme, abyste šifrovací klíč dat služby změnili ročně, aby se zabránilo ohrožení bezpečnosti klíčů.

## <a name="protect-data-at-rest"></a>Ochrana neaktivních uložených dat

Zařízení StorSimple spravuje data tak, že je ukládá do vrstev místně a v cloudu v závislosti na frekvenci použití. Všechny hostitelské počítače, které jsou připojené k zařízení, odesílají data do zařízení, které pak v případě potřeby přesouvá data do cloudu. Data se ze zařízení přenáší zabezpečeně prostřednictvím Internetu. Každé zařízení má jeden cíl iSCSI, který na tomto zařízení nasdílí všechny sdílené svazky. Všechna data se šifrují předtím, než se odešlou do cloudového úložiště. 

![Šifrovací klíč cloudového úložiště](./media/storsimple-security/CloudStorageEncryption.png)

K zajištění zabezpečení a integrity dat přesunutých do cloudu vám StorSimple umožňuje definovat šifrovací klíče cloudového úložiště následujícím způsobem:

* Šifrovací klíč cloudového úložiště určíte při vytváření kontejneru svazků. Klíč nelze upravit ani přidat později.
* Všechny svazky v kontejneru svazků sdílejí stejný šifrovací klíč. Pokud chcete pro určitý svazek použít jinou formu šifrování, doporučujeme pro hostování tohoto svazku vytvořit nový kontejner svazků.
* Když zadáte šifrovací klíč cloudového úložiště ve službě StorSimple Device Manager, klíč se zašifruje pomocí veřejné části šifrovacího klíče dat služby a pak se pošle do zařízení.
* Šifrovací klíč cloudového úložiště není uložen kdekoli ve službě a je známý jenom pro zařízení.
* Zadání šifrovacího klíče cloudového úložiště je volitelné. Do zařízení můžete odesílat data zašifrovaná v hostiteli.

### <a name="additional-security-best-practices"></a>Další osvědčené postupy zabezpečení

* Rozdělení provozu: izolování sítě SAN iSCSI od uživatelského provozu v podnikové síti LAN nasazením zcela oddělené sítě a pomocí sítí VLAN, kde fyzickou izolaci není možnost. Vyhrazená síť pro úložiště iSCSI zaručuje bezpečnost a výkon důležitých podnikových dat. Kombinování úložiště a uživatelských přenosů přes podnikovou síť LAN se nedoporučuje a může zvýšit latenci a způsobit selhání sítě.
* U zabezpečení sítě na straně hostitele použijte síťová rozhraní, která podporují modul TOE (TCP/IP Offload Engine). TOE snižuje zatížení procesoru zpracováním protokolu TCP na síťovém adaptéru.

## <a name="protect-data-via-storage-accounts"></a>Ochrana dat prostřednictvím účtů úložiště

Každý Microsoft Azure předplatné může vytvořit jeden nebo více účtů úložiště. (Účet úložiště poskytuje jedinečný obor názvů pro práci s daty uloženými v cloudu Azure.) Přístup k účtu úložiště řídí předplatné a přístupové klíče přidružené k tomuto účtu úložiště.

Když vytváříte účet úložiště, Microsoft Azure generuje 2 512 klíče pro přístup k úložišti, jedna z nich se používá k ověřování, když zařízení StorSimple přistupuje k účtu úložiště. Všimněte si, že se používá pouze jeden z těchto klíčů. Druhý klíč je držen v rezervě, což vám umožní pravidelně střídat klíče. Chcete-li otočit klíče, nastavte sekundární klíč jako aktivní a pak odstraňte primární klíč. Pak můžete vytvořit nový klíč pro použití při dalším otočení. (Z bezpečnostních důvodů vyžaduje mnoho datových center rotaci klíčů.)

Doporučujeme dodržovat tyto osvědčené postupy pro rotaci klíčů:

* Klíče účtu úložiště byste měli pravidelně otáčet, abyste měli jistotu, že k účtu úložiště nezískají přístup neoprávnění uživatelé.
* Správce Azure by měl pravidelně měnit nebo obnovovat primární nebo sekundární klíč pomocí části úložiště Azure Portal pro přímý přístup k účtu úložiště.

## <a name="protect-data-via-encryption"></a>Ochrana dat pomocí šifrování

StorSimple používá následující šifrovací algoritmy k ochraně dat, která jsou uložená v nebo na cestách mezi součástmi vašeho řešení StorSimple.

| Algoritmus | Délka klíče | Protokoly/aplikace/komentáře |
| --- | --- | --- |
| RSA |2 048 |RSA PKCS 1 v 1.5 používá Azure Portal k šifrování konfiguračních dat, která se odesílají do zařízení: například přihlašovací údaje účtu úložiště, konfigurace zařízení StorSimple a šifrovací klíče cloudového úložiště. |
| AES |256 |AES with CBC slouží k šifrování veřejné části šifrovacího klíče dat služby předtím, než se pošle do Azure Portal ze zařízení StorSimple. Používá ho i zařízení StorSimple k šifrování dat předtím, než se data odešlou do účtu cloudového úložiště. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance zabezpečení

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Správa osobních údajů

StorSimple Device Manager pro fyzické i virtuální řady shromažďuje osobní informace v následujících klíčových instancích:

- Upozornění na uživatelská nastavení, kde jsou nakonfigurovaná e-mailová adresa uživatelů Tyto informace může správce zobrazit a vymazat. To platí pro zařízení řady StorSimple 8000 i pro virtuální pole StorSimple.
  * Pokud chcete zobrazit a vymazat nastavení pro řadu StorSimple 8000, postupujte podle kroků v části [zobrazení a správa výstrah StorSimple.](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Pokud chcete zobrazit a vymazat nastavení pro virtuální pole StorSimple, postupujte podle kroků v části [zobrazení a správa výstrah StorSimple.](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Uživatelé, kteří mají přístup k datům umístěným ve sdílených složkách. Zobrazí se seznam uživatelů, kteří mají přístup ke sdíleným datům, a lze je zobrazit. Tento seznam se odstraní také při odstranění sdílených složek. To platí jenom pro virtuální pole StorSimple.
  * Pokud chcete zobrazit seznam uživatelů, kteří mají přístup k nebo odstranit sdílenou složku, postupujte podle kroků v části [Správa sdílených složek ve virtuálním poli StorSimple](storsimple-virtual-array-manage-shares.md) .

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Tady jsou uvedené otázky a odpovědi týkající se zabezpečení a Microsoft Azure StorSimple.

**Otázka:** Dojde k ohrožení bezpečnosti mé služby. Jak by se měly provést další kroky?

**A:** Měli byste hned změnit šifrovací klíč dat služby a klíče účtu úložiště pro účet úložiště, který se používá pro data vrstvení. Pokyny najdete tady:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Střídání klíčů účtů úložiště](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Otázka:** Mám nové zařízení StorSimple, které žádá o registrační klíč služby. Návody ho načíst?

**A:** Tento klíč byl vytvořen při prvním vytvoření služby StorSimple Device Manager. Když ke svému zařízení použijete službu StorSimple Device Manager, můžete k zobrazení nebo opětovnému vygenerování registračního klíče služby použít stránku rychlý Start. Generování nového registračního klíče služby nebude mít vliv na existující registrovaná zařízení. Pokyny najdete tady:

* [Zobrazit nebo znovu vygenerovat registrační klíč služby](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**Otázka:** Ztratil (a) jsem svůj šifrovací klíč dat služby. Co mám udělat?

**A:** Kontaktujte podpora Microsoftu. Můžou se přihlásit k relaci podpory na vašem zařízení a pomůžou vám načíst klíč (Pokud je aspoň jedno zařízení online). Ihned po získání šifrovacího klíče dat služby byste ho měli změnit, abyste měli jistotu, že je nový klíč známý jenom pro vás. Pokyny najdete tady:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Otázka:**  Povolil (a) jsem zařízení pro změnu šifrovacího klíče dat služby, ale nespustila proces změny klíče. Co bych měl/a dělat?

**A:** Pokud časový limit vypršel, budete muset znovu autorizovat zařízení pro změnu šifrovacího klíče dat služby a znovu spustit proces.

**Otázka:**  Změnil (a) jsem šifrovací klíč dat služby, ale nedokázali jsme aktualizovat ostatní zařízení během 4 hodin. Musím znovu začít?

**A:** Časové období ve 4 hodinách je pouze pro zahájení změny. Po zahájení procesu aktualizace na autorizovaných zařízeních StorSimple je autorizace platná, dokud nebudou všechna zařízení aktualizována.

**Otázka:** Náš správce StorSimple opustil společnost. Co bych měl/a dělat?

**A:** Změna a resetování hesel, která umožňují přístup k zařízení StorSimple, a změna šifrovacího klíče dat služby, aby bylo zajištěno, že nové informace nebudou známy neoprávněným osobám. Pokyny najdete tady:

* [Použijte službu StorSimple Device Manager ke změně hesla StorSimple.](storsimple-8000-change-passwords.md)
* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md)

**Otázka:** Chci zadat Snapshot Manager heslo StorSimple k hostiteli, který se připojuje k zařízení StorSimple, ale heslo není k dispozici. Co mám udělat?

**A:** Pokud jste zapomněli heslo, měli byste vytvořit nové. Pak se ujistěte, že všichni stávající uživatelé změnili heslo a že by měli aktualizovat své klienty, aby používali nové heslo. Pokyny najdete tady:

* [Změna StorSimple hesla Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Ověření zařízení](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Otázka:** V zařízení se změnil certifikát pro vzdálený přístup k Windows PowerShell pro StorSimple. Návody aktualizovat klienty vzdáleného přístupu?

**A:** Můžete si stáhnout nový certifikát ze služby StorSimple Device Manager a pak ho poskytnout do úložiště certifikátů klientů vzdáleného přístupu. Pokyny najdete tady:

* [Rutina Import-certifikát](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**Otázka:** Je moje data chráněná, pokud dojde k ohrožení bezpečnosti služby StorSimple Device Manager?

**A:** Data konfigurace služby se při prohlížení ve webovém prohlížeči vždycky šifrují s veřejným klíčem. Vzhledem k tomu, že služba nemá přístup k privátnímu klíči, služba nebude moci zobrazit žádná data. Pokud dojde k ohrožení bezpečnosti služby StorSimple Device Manager, nebudete mít žádný vliv, protože ve službě StorSimple Device Manager nejsou uložené žádné klíče.

**Otázka:** Pokud někdo získá přístup k certifikátu pro šifrování dat, budou se tato data ohrozit?

**A:** Microsoft Azure ukládá datový šifrovací klíč (soubor. pfx) zákazníka v zašifrovaném formátu. Vzhledem k tomu, že je soubor. pfx zašifrovaný a Služba StorSimple nemá šifrovací klíč dat služby k dešifrování souboru. pfx, jednoduše získáte přístup k souboru. pfx, aby se nezveřejnily žádné tajné kódy.

**Otázka:** Co se stane, když si vládní entita požádá společnost Microsoft o moje data?

**A:** Vzhledem k tomu, že jsou všechna data ve službě zašifrovaná a soukromý klíč je uložený v zařízení, musí se tato entita požádat zákazníka o data.



## <a name="next-steps"></a>Další kroky

[Nasaďte zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

