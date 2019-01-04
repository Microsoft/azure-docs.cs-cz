---
title: StorSimple 8000 series zabezpečení | Dokumentace Microsoftu
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chrání služby StorSimple, zařízení a dat v místním prostředí i v cloudu.
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
ms.openlocfilehash: 734b0cf9373ea98ab33c06b45ad53b46a3355dd6
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976014"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple zabezpečení a ochranu dat.

## <a name="overview"></a>Přehled

Zabezpečení je hlavním zájmem zajistit u každého, kdo zavádí nová technologie, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Při hodnocení různých technologií, je nutné zvážit zvýšené riziko a náklady na ochranu dat. Microsoft Azure StorSimple nabízí zabezpečení a ochrany osobních údajů řešení pro ochranu dat, pomáhá zajistit:

* **Důvěrnost** – jen autorizované entity můžete zobrazit vaše data.
* **Integrita** – můžete upravit nebo odstranit data jenom autorizované entity.

Řešení Microsoft Azure StorSimple se skládá z čtyři hlavní součásti, které spolu interagují:

* **Služba Správce zařízení StorSimple, které jsou hostované v Microsoft Azure** – službu pro správu, který použijete ke konfiguraci a zřízení zařízení StorSimple.
* **Zařízení StorSimple** – fyzických zařízení nainstalované ve vašem datovém centru. Všechny hostitele a klienty, které generují data připojení k zařízení StorSimple a zařízení spravuje data a přesouvá ji do cloudu Azure podle potřeby.
* **Klienty a hostitele připojené k zařízení** – klienti ve vaší infrastruktuře, připojte se k zařízení StorSimple, která generují data, která se dají chránit.
* **Cloudové úložiště** – umístění v cloudu Azure, kam se data uloží.

Následující části popisují funkce zabezpečení StorSimple, které pomáhají chránit každá z těchto komponent a data uložená na ně. Obsahuje také seznam dotazů, které můžete mít informace o zabezpečení Microsoft Azure StorSimple a příslušné odpovědi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrana služby Správce zařízení StorSimple

Služba Správce zařízení StorSimple je služba pro správu hostované v Microsoft Azure a použít ke správě všech zařízení StorSimple, které zprostředkoval vaší organizace. Služba Správce zařízení StorSimple můžete přistupovat pomocí firemní přihlašovací údaje pro přihlášení k webu Azure portal prostřednictvím webového prohlížeče.

Přístup ke službě Správce zařízení StorSimple vyžaduje vaše organizace předplatné Azure, které zahrnuje StorSimple. Vaše předplatné určuje funkce, které máte na webu Azure Portal dostupné. Pokud vaše organizace nemá předplatné služby Azure a chcete další informace o nich najdete v tématu [zaregistrovat do Azure jako organizace](../active-directory/fundamentals/sign-up-organization.md).

Vzhledem k tomu, že služba Správce zařízení StorSimple je hostovaná v Azure, je chráněn funkce zabezpečení Azure. Další informace o funkcích zabezpečení poskytovaných v prostředí Microsoft Azure najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrana zařízení StorSimple

Zařízení StorSimple je zařízení místní hybridní úložiště, který obsahuje jednotkami SSD (Solid-State Drive) a jednotky pevného disku (HDD), s redundantní řadiče a automatické převzetí služeb při selhání. Kontrolery spravovat úložiště ovládání datových vrstev, umístění aktuálně používá (nebo hot) dat v místním úložišti (v StorSimple zařízení nebo na místní servery), při přesouvání méně často používaná data do cloudu.

Oprávnění jenom zařízení se mohou připojit službu Správce zařízení StorSimple, kterou jste vytvořili ve vašem předplatném Azure StorSimple. K ověření zařízení, zaregistrujte se službou StorSimple Device Manager tím, že poskytuje registrační klíč služby. Registrační klíč služby je 128-bit náhodný klíč vygenerovaný na webu Azure Portal.

![registrační klíč služby](./media/storsimple-security/ServiceRegistrationKey.png)

Další informace jak získat registrační klíč, přejděte na [krok 2: Získání registračního klíče služby](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Registrační klíč služby je dlouhý klíč, který obsahuje více než 100 znaků. Můžete zkopírovat klíč a uložte ho do textového souboru v zabezpečeném umístění tak, aby ho můžete používat k autorizaci další zařízení podle potřeby. Pokud po registraci prvního zařízení dojde ke ztrátě registrační klíč služby, můžete vygenerovat nový klíč ze služby Správce zařízení StorSimple. To nebude mít vliv na fungování stávajících zařízení.

Jakmile je zařízení registrováno, používá tokeny ke komunikaci s Microsoft Azure. Po registraci zařízení se nepoužívá registrační klíč služby.

> [!NOTE]
> Doporučujeme, abyste po každé použití znovu vygenerovat registrační klíč služby.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Ochranu vašeho řešení StorSimple pomocí hesel

Hesla jsou důležitou součástí zabezpečení počítače a jsou často používány v řešení StorSimple k zajištění, že vaše data jsou přístupné pouze oprávněným uživatelům. StorSimple umožňuje nakonfigurovat následující hesla:

* Heslo správce zařízení StorSimple
* Výzva ověřování protokol CHAP (Handshake) a iniciátor hesla
* Heslo správce Snapshot Manageru zařízení StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Prostředí Windows PowerShell pro StorSimple a heslo správce zařízení StorSimple

Prostředí Windows PowerShell pro StorSimple je rozhraní příkazového řádku, který můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple je funkce, které umožňují zaregistrovat své zařízení, konfigurace síťového rozhraní na zařízení, nainstalovat určité typy aktualizací, díky přístupu do relace podporu potíží se zařízením a změnit stav zařízení. Připojení ke konzole sériového portu zařízení nebo pomocí vzdálené komunikace Windows Powershellu, lze použít prostředí Windows PowerShell pro StorSimple.

Vzdálená komunikace Powershellu je možné provést prostřednictvím protokolu HTTPS nebo HTTP. Pokud je povolená Vzdálená správa přes protokol HTTPS, je potřeba stáhnout certifikát pro vzdálenou správu ze zařízení a nainstalujte ho na vzdáleném klientovi. Další informace o vzdálené komunikace Powershellu najdete v části [připojit vzdáleně ke svému zařízení StorSimple](storsimple-8000-remote-connect.md).

Po připojení k zařízení pomocí Windows Powershellu pro StorSimple, budete potřebovat k zadání hesla správce zařízení pro přihlášení k zařízení.

![Heslo správce zařízení](./media/storsimple-security/DeviceAdminPW.png)

Následující osvědčené postupy mějte na paměti:

* Vzdálená správa je vypnuto ve výchozím nastavení. Služba Správce zařízení StorSimple můžete ji povolit. Z hlediska zabezpečení je nejvhodnější vzdáleného přístupu by měla být povolená pouze během časového období, která je skutečně potřeba.
* Pokud změníte heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, aby nesetkávají ztrátou připojení k neočekávané.
* Služba Správce zařízení StorSimple nejde načíst existující hesla: to je jenom resetování. Doporučujeme, abyste tak, že není potřeba resetovat heslo, pokud je zapomenuté ukládání všech hesel na bezpečném místě. Pokud potřebujete resetovat heslo, nezapomeňte informovat všechny uživatele před jeho resetování.

Rozhraní Windows PowerShell můžete přistupovat pomocí sériové připojení k zařízení. Můžete také přistupovat ho vzdáleně pomocí protokolu HTTP nebo HTTPS, který poskytuje dodatečné zabezpečení. Protokol HTTPS nabízí vyšší úroveň zabezpečení než sériové nebo připojení HTTP. Ale pro použití protokolu HTTPS, nejprve nainstalujte certifikát na klientském počítači, který bude přístup k zařízení. Certifikát pro vzdálený přístup můžete stáhnout na stránce konfigurace zařízení ve službě Správce zařízení StorSimple. Pokud dojde ke ztrátě certifikátu pro vzdálený přístup, je nutné stáhnout nový certifikát a rozšíří na všechny klienty, kteří jsou oprávněni používat vzdálenou správu.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Výzva ověřování protokol CHAP (Handshake) a iniciátor hesla

Protokol CHAP je schéma ověřování použité v zařízení StorSimple pro ověření identity vzdálených klientů. Ověření je založená na sdílené heslo. Protokol CHAP mohou být jednosměrné (jednosměrných) nebo vzájemné (obousměrné). S jednosměrnou CHAP ověřuje cíl (zařízení StorSimple) iniciátor (hostitel). Vzájemné nebo reverzního CHAP vyžaduje, že cíl ověřování iniciátor a iniciátor ověří cíl. StorSimple může nakonfigurován k používání některé z metod.

Mějte na paměti z následujících akcí při konfiguraci protokolu CHAP:

* Uživatelské jméno CHAP musí obsahovat méně než 233 znaků.
* Heslo CHAP musí být 12 až 16 znaků. Pokus o použít delší uživatelské jméno nebo heslo povede selhání ověřování Windows.
* Pro iniciátor protokolu CHAP a cíle CHAP nelze použít stejné heslo.
* Po nastavení hesla, lze jej změnit, ale nelze načíst. Pokud se změní heslo, nezapomeňte informovat všechny uživatele vzdáleného přístupu tak, aby mohli úspěšně připojit k zařízení StorSimple.

Další informace o protokolu CHAP a jak ho nakonfigurovat pro vaše řešení StorSimple, přejděte na [konfigurace CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Heslo správce Snapshot Manageru zařízení StorSimple

StorSimple Snapshot Manageru je modul snap-in konzoly Microsoft Management Console (MMC), který používá skupin svazků a služby Stínová kopie svazku Windows k vygenerování zálohy konzistentní s aplikací. Kromě toho můžete použít StorSimple Snapshot Manageru vytvořit plány zálohování a klonování nebo obnovte svazky.

Při konfiguraci zařízení StorSimple Snapshot Manageru budete muset k zadání hesla Snapshot Manageru zařízení StorSimple. Toto heslo se nejprve nastavit v prostředí Windows PowerShell pro StorSimple během registrace. Heslo můžete také nastavit a změnit ze služby Správce zařízení StorSimple. Toto heslo se ověřuje pomocí StorSimple Snapshot Manageru zařízení.

![Heslo správce Snapshot Manageru zařízení StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple Snapshot Manageru heslo musí obsahovat 14 až 15 znaků a musí obsahovat 3 nebo více kombinací velká písmena, malá písmena, číselné a speciální znaky. Po nastavení hesla Snapshot Manageru zařízení StorSimple, lze jej změnit, ale nelze načíst. Pokud změníte heslo, je nutné upozornit všichni vzdálení uživatelé.

Další informace o StorSimple Snapshot Manageru, přejděte na [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Osvědčené postupy pro hesla

Doporučujeme vám pomáhají zajistit, že hesel zařízení StorSimple jsou silné a dobře chráněné pomocí následujících pokynů:

* Změna hesla každé tři měsíce. Změna hesla se pořizují na rok vynucuje.
* Použijte silná hesla. Další informace najdete v části [vytvořit volba bezpečnějších hesel a chránit je](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Vždy používejte různá hesla pro přístup k jiné mechanismy; Každý z hesla, které zadáte, musí být jedinečné.
* Nesdílejte hesla s kýmkoli, kdo nemá oprávnění k přístupu k zařízení StorSimple.
* Mluví o zadání hesla před ostatními nebo pomocného parametru na formát hesla.
* Pokud máte podezření, že účtu nebo hesla došlo k napadení, ohlaste incident na vaše oddělení zabezpečení informací.
* Všechna hesla považovat za důvěrné, citlivé informace. 

## <a name="storsimple-data-protection"></a>Ochrana dat StorSimple

Tato část popisuje funkce zabezpečení StorSimple, které chrání přenášená data a uložená data.

Jak je popsáno v dalších částech, hesla se používají k autorizujte a ověřujte uživatele předtím, než získají přístup k řešení StorSimple. Dalším aspektem zabezpečení je data byla chráněná před neoprávněným uživatelům při se přenosu mezi úložných systémů a při jeho uložení. Následující části popisují funkce pro ochranu dat, opatřeného StorSimple.

> [!NOTE]
> Odstranění duplicitních dat zajišťuje zvýšenou ochranu pro data uložená na zařízení StorSimple a Microsoft Azure storage. Když data se odstraňují duplicity, datové objekty, které se ukládají odděleně od metadat používají ke zmapování a přistupovat k nim: neexistuje žádný k dispozici kontext úroveň úložiště k rekonstrukci dat na základě struktury svazku, systém souborů nebo název souboru.


## <a name="protect-data-flowing-through-the-service"></a>Ochrana dat přes službu

Správa a konfigurace zařízení StorSimple je hlavním účelem služby Správce zařízení StorSimple. Služba Správce zařízení StorSimple běží v Microsoft Azure. Zadejte data konfigurace zařízení pomocí webu Azure portal a použije ve službě Správce zařízení StorSimple k odesílání dat do zařízení v Microsoft Azure. StorSimple využívá systém asymetrického klíče dvojice pomáhá zajistit, že ohrožení zabezpečení služby Azure nebude mít za následek ohrožení zabezpečení uložené informace.

![Šifrování dat na cestě](./media/storsimple-security/DataEncryption.png)

Asymetrické klíče systému k ochraně dat, která prochází přes službu následujícím způsobem:

1. Certifikát šifrování dat, která používá asymetrický klíč veřejné a soukromé dvojice je generovaný na zařízení a slouží k ochraně dat. Klíče jsou generovány, pokud je první zařízení zaregistrované.
2. Certifikát šifrovací klíče dat, jsou exportovány do souboru Personal Information Exchange (.pfx), který je chráněn šifrovací klíč dat služby, který je silný klíč 128-bit, který náhodně generovaných první zařízení během registrace.
3. Veřejný klíč certifikátu je bezpečně k dispozici ve službě Správce zařízení StorSimple a privátní klíč zůstane se zařízením.
4. Zadání službu data zašifrovaná pomocí veřejného klíče a je dešifrován pomocí soukromého klíče uložené na zařízení a zajištění, že služba Azure nejde dešifrovat dat odesílaných do zařízení.

Šifrovací klíč dat služby se vygeneruje jenom první zařízení zaregistrovaná ve službě service. Všechna další zařízení, které jsou registrované ve službě, musíte použít stejné šifrovací klíč dat služby.

> [!IMPORTANT]
> Je velmi důležité vytvořit kopii šifrovacího klíče dat služby a uložte ho na bezpečném místě. Kopii šifrovacího klíče dat služby uskladněny takovým způsobem, že je přístupný autorizované osoby a můžete snadno sdělí správce zařízení.
> 
> Pokud dojde ke ztrátě šifrovací klíč dat služby, pracovníci technické podpory Microsoftu vám mohou pomoci při jeho načtení za předpokladu, že máte alespoň jedno zařízení ve stavu online. Doporučujeme změnit šifrovací klíč dat služby, jakmile jsou načteny.

Chcete-li změnit šifrovací klíč dat služby a odpovídající certifikát šifrování dat, postupujte podle kroků v [změnit šifrovací klíč dat služby pro službu Správce zařízení StorSimple](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Změna šifrovací klíče vyžaduje, aby všechna zařízení aktualizovat s novým klíčem. Proto doporučujeme, změňte klíč všechna zařízení jsou online. Pokud je zařízení offline, jejich klíčů lze změnit v jinou dobu. Zařízení se zastaralým klíče budou moct dál spuštění zálohování, ale nebudou schopni obnovit data až po aktualizaci klíče.

Šifrovací klíč dat služby a data šifrovací certifikát, nevyprší platnost. Doporučujeme však, že změníte šifrovací klíč dat služby každoročně, aby se zabránilo ohrožení bezpečnosti klíčů.

## <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

Zařízení StorSimple spravuje data pomocí ukládání ve vrstvách místně i v cloudu, v závislosti na četnosti použití. Všechny hostitelské počítače, které jsou připojené k zařízení odesílat data do zařízení, které pak přesouvá data do cloudu a podle potřeby. Data se ze zařízení do cloudu přenáší bezpečně prostřednictvím Internetu. Každé zařízení má jeden cíl iSCSI, který poskytuje informace o všech sdílených svazků na tomto zařízení. Všechna data jsou zašifrována před zasláním do cloudového úložiště. 

![šifrovací klíč cloudového úložiště](./media/storsimple-security/CloudStorageEncryption.png)

K zajištění zabezpečení a integrity dat přesunout na cloud, StorSimple umožňuje definovat cloudové úložiště šifrovacích klíčů následujícím způsobem:

* Šifrovací klíč cloudového úložiště se zadávají při vytváření kontejneru svazků. Klíč nelze později přidá nebo upraví.
* Všechny svazky v kontejneru svazků sdílet stejný šifrovací klíč. Pokud chcete jinou formu šifrování pro určitý svazek, doporučujeme vytvořit nový kontejner svazků pro hostování tohoto svazku.
* Pokud zadáte šifrovací klíč cloudového úložiště ve službě Správce zařízení StorSimple, že je klíč zašifrovaný s využitím veřejné části šifrovací klíč dat služby a potom je odešlete do zařízení.
* Šifrovací klíč cloudového úložiště není uložené kdekoli ve službě a znáte jenom zařízení.
* Šifrovací klíč cloudového úložiště zadání je volitelné. Můžete odeslat data, která byla dříve zašifrována na hostitele tak, aby zařízení.

### <a name="additional-security-best-practices"></a>Osvědčené postupy pro zvýšení zabezpečení

* Rozdělení provozu: nasazení úplně oddělené sítě a pomocí sítí VLAN, kde fyzické izolace není možné izolovat vaše síť SAN iSCSI z uživatelského provozu v podnikové síti LAN. Vyhrazené sítě úložiště iSCSI se zajištění bezpečnosti a výkonu důležitá obchodní data. Kombinování provoz úložiště a uživatelů v podnikové síti LAN se nedoporučuje a můžou zvýšit latenci a dojít k chybám sítě.
* Zabezpečení sítě na straně hostitele použijte síťová rozhraní, které podporují protokol TCP/IP snižování zátěže Engine (PORÁŽKY). PORÁŽKY snižuje zatížení procesoru v síťovém adaptéru zpracování protokolu TCP.

## <a name="protect-data-via-storage-accounts"></a>Ochrana dat přes účet úložiště

Každé předplatné Microsoft Azure můžete vytvořit jeden nebo více účtů úložiště. (Účet služby storage poskytuje jedinečný obor názvů pro práci s daty uloženými v cloudu Azure.) Předplatné a přístupových klíčů, které jsou přidružené k tomuto účtu úložiště řídí přístup k účtu úložiště.

Při vytváření účtu úložiště vygeneruje Microsoft Azure dva 512bitové přístupových klíčů k úložišti, z nichž jeden se používá k ověřování, když zařízení StorSimple, získá přístup k účtu úložiště. Všimněte si, že se používá jenom jedna z těchto klíčů. Další klíč se nachází ve fondu, abyste mohli klíče pravidelně otočit. Obměna klíčů, nastavíte jako aktivní sekundární klíč a pak odstraňte primární klíč. Můžete si vytvořit nový klíč pro použití během další obměnu. (Z bezpečnostních důvodů vyžadují mnoho datových centrech obměny klíče.)

Doporučujeme, abyste postupovali podle těchto osvědčených postupů pro obměna klíčů:

* By měl obměna klíčů účtu úložiště pravidelně k zajištění, že váš účet úložiště není přístup neoprávnění uživatelé.
* Správce Azure by měl pravidelně, změnit nebo znovu vygenerovat primární nebo sekundární klíč pomocí části úložiště na webu Azure portal pro přímý přístup k účtu úložiště.

## <a name="protect-data-via-encryption"></a>Ochrana dat pomocí šifrování

StorSimple využívá tyto algoritmy šifrování chránit data uložená v nebo na cestách mezi součástmi řešení StorSimple.

| algoritmus | Délka klíče | Protokoly/aplikace/komentáře |
| --- | --- | --- |
| RSA |2 048 |V1.5 RSA PKCS č. 1 na webu Azure portal používá k šifrování konfiguračních dat, která se odešle do zařízení: například přihlašovací údaje, konfigurace zařízení StorSimple, účtu úložiště a cloudové úložiště šifrovacích klíčů. |
| AES |256 |AES s CBC se používá k šifrování veřejnou část šifrovací klíč dat služby před odesláním k webu Azure portal ze zařízení StorSimple. Také se používá v zařízení StorSimple k šifrování dat před odesláním dat do účtu cloudového úložiště. |

## <a name="storsimple-cloud-appliance-security"></a>Zabezpečení řešení StorSimple Cloud Appliance

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Správa osobních údajů

Správce zařízení StorSimple pro fyzické i virtuální řady shromažďuje osobní údaje v následující instance klíče:

- Pošle upozornění, uživatelská nastavení, které jsou nakonfigurované e-mailovou adresu uživatele. Tyto informace můžete zobrazit a zrušeno správcem. To platí pro zařízeních StorSimple řady 8000 a virtuálních polí StorSimple.
  * K zobrazení a vymažte nastavení pro řadu StorSimple 8000, postupujte podle kroků v [zobrazení a Správa výstrah StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * K zobrazení a vymažte nastavení pro StorSimple Virtual Array, postupujte podle kroků v [zobrazení a Správa výstrah StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Uživatelé, kteří můžou přistupovat k datům na sdílené složky. Seznam uživatelů, kteří můžou přistupovat k datům sdílené složky se zobrazí a je možné zobrazit. Tento seznam se také odstraní při odstranění sdílené složky. To platí jenom pro virtuální pole StorSimple.
  * Chcete-li zobrazit seznam uživatelů, kdo může přistupovat nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky na StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)

Další informace najdete v Zásadách ochrany osobních údajů společnosti Microsoft v [Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Následují některé otázky a odpovědi týkající se zabezpečení a Microsoft Azure StorSimple.

**DOTAZ:** Služba dojde k narušení. Co by měl být Moje další kroky?

**ODPOVĚĎ:** Měli byste hned změnit šifrovací klíč dat služby a klíče účtu úložiště pro účet úložiště, který se používá pro vrstvení dat. Pokyny najdete v tématu:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Obměna klíčů účtů úložiště](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**DOTAZ:** Mám k dispozici nové zařízení StorSimple, která žádá o registrační klíč služby. Jak ji mohu načíst?

**ODPOVĚĎ:** Tento klíč byl vytvořen při prvním vytvoření ve službě Správce zařízení StorSimple. Při použití služby Správce zařízení StorSimple pro připojení k zařízení, můžete použít na úvodní stránku služby k zobrazení nebo znovu vygenerovat registrační klíč služby. Generuje se nový registrační klíč služby neovlivní existující zaregistrovaná zařízení. Pokyny najdete v tématu:

* [Zobrazení nebo znovu vygenerovat registrační klíč služby](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**DOTAZ:** Mohu ztratit Moje šifrovací klíč dat služby. Co mám udělat?

**ODPOVĚĎ:** Obraťte se na podporu Microsoftu. Se můžou přihlašovat do relace podporu na zařízení a nápovědu načíst klíč (za předpokladu, že je online alespoň jedno zařízení). Ihned poté, co můžete získat šifrovací klíč dat služby, měli byste změnit jej pro zajištění, že nový klíč znáte jenom vy. Pokyny najdete v tématu:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**DOTAZ:**  Můžu oprávnění zařízení pro změnu klíče šifrování dat služby, ale není začal proces změny klíče. Co bych měl/a dělat?

**ODPOVĚĎ:** Pokud časový limit vyprší, je potřeba autorizovat zařízení pro změnu klíče šifrování dat služby a znovu ji spusťte proces.

**DOTAZ:**  Můžu změnit šifrovací klíč dat služby, ale jsem se nepodařilo aktualizovat na ostatních zařízeních do 4 hodin. Budu muset znovu spustit?

**ODPOVĚĎ:** 4 hodiny časové období se pouze za inicializaci změny. Po spuštění procesu aktualizace na autorizované zařízení StorSimple autorizace je platná, dokud nebudou aktualizovány všechna zařízení.

**DOTAZ:** Naše správce StorSimple opustil společnost. Co bych měl/a dělat?

**ODPOVĚĎ:** Změnit a resetovat hesla, které umožňují přístup k zařízení StorSimple a změnit šifrovací klíč dat služby k zajištění, že není znám nové informace neoprávněným osobám. Pokyny najdete v tématu:

* [Použití služby Správce zařízení StorSimple, chcete-li změnit hesla služby storsimple](storsimple-8000-change-passwords.md)
* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurace protokolu CHAP pro zařízení StorSimple](storsimple-8000-configure-chap.md)

**DOTAZ:** Chci, aby k zadání hesla Snapshot Manageru zařízení StorSimple na hostitele, který se připojuje k zařízení StorSimple, ale heslo není k dispozici. Co můžu udělat?

**ODPOVĚĎ:** Pokud jste zapomněli heslo, měli byste vytvořit nový. Pak nezapomeňte informovat všechny existující uživatele, že se změnil heslo a že by měl aktualizují jejich klientů na používání nové heslo. Pokyny najdete v tématu:

* [Změna hesla Snapshot Manageru zařízení StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Ověřování zařízení](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**DOTAZ:** Certifikát pro vzdálený přístup k prostředí Windows PowerShell pro StorSimple se změnil na zařízení. Jak můžu aktualizovat mé klienty vzdáleného přístupu?

**ODPOVĚĎ:** Můžete stáhnout nový certifikát ze služby Správce zařízení StorSimple a potom zadejte, aby byl nainstalován v úložišti certifikátů klienty vzdáleného přístupu. Pokyny najdete v tématu:

* [Rutina Import-Certificate](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**DOTAZ:** Je moje data chráněných Pokud dojde k narušení služby Správce zařízení StorSimple?

**ODPOVĚĎ:** Konfigurační data služby je vždy šifrován veřejný klíč, při zobrazení ve webovém prohlížeči. Vzhledem k tomu, že služba nemá přístup k privátnímu klíči, služba nebude moci zobrazit žádná data. Pokud dojde k narušení služby Správce zařízení StorSimple, neexistuje žádný vliv, protože neexistují žádné klíče uložené ve službě Správce zařízení StorSimple.

**DOTAZ:** Pokud někdo získá přístup k certifikátu šifrování dat, bude Moje data ohrožena?

**ODPOVĚĎ:** Microsoft Azure ukládá šifrovací klíč dat zákazníka (soubor .pfx) v zašifrovaném formátu. Protože soubor PFX je zašifrovaný a služby StorSimple nemá šifrovací klíč dat služby pro dešifrování souboru .pfx, nebude jednoduše získal přístup k souboru PFX vystavení jakýchkoli tajných kódů.

**DOTAZ:** Co se stane, když instituci státní požádá společnost Microsoft o Moje data?

**ODPOVĚĎ:** Protože veškerá data se šifrují ve službě a privátní klíč se uchovává se zařízením, musíte požádat instituci státní zákazníka pro data.



## <a name="next-steps"></a>Další postup

[Nasazení zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

