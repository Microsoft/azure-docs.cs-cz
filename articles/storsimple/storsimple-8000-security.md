---
title: Zabezpečení řady StorSimple 8000 | Microsoft Docs
description: Popisuje funkce zabezpečení a ochrana osobních údajů, které chránit služby StorSimple, zařízení a data místně a v cloudu.
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
ms.date: 03/27/2018
ms.author: alkohli
ms.openlocfilehash: ee0534ab8bc3400854ddf550df9ca2f395f8d80d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple zabezpečení a ochranu dat.

## <a name="overview"></a>Přehled

Zabezpečení představuje závažný problém pro každého, kdo používá novou technologii, zejména v případě, že tato technologie se používá s důvěrných nebo vlastnických dat. Jak můžete vyhodnotit různých technologií, musíte zvážit, zvýšená rizika a náklady na ochranu dat. Microsoft Azure StorSimple poskytuje zabezpečení a ochrany osobních údajů řešení pro ochranu dat, pomáhá zajistit:

* **Důvěrnost** – pouze autorizovaný entity můžete zobrazit vaše data.
* **Integrita** – můžete upravit nebo odstranit data jenom autorizovaní entity.

Řešení Microsoft Azure StorSimple zahrnuje čtyři hlavní součásti, které spolupracují mezi sebou:

* **Správce zařízení StorSimple služby hostované v Microsoft Azure** – služby správy, která můžete použít ke konfiguraci a zřízení zařízení StorSimple.
* **Zařízení StorSimple** – fyzických zařízení nainstalované ve vašem datovém centru. Všechny hostitele a klienty, které generují data se připojte k zařízení StorSimple a zařízení spravuje data a přesouvá ji do cloudu Azure podle potřeby.
* **Klienti nebo hostitele připojeného k zařízení** – klienti ve vaší infrastruktuře, které se připojují k zařízení StorSimple a vygenerovat data, která je potřeba chránit.
* **Cloudového úložiště** – umístění v cloudu Azure, které jsou uložená data.

Následující části popisují StorSimple funkce zabezpečení, které pomáhají chránit všechny tyto součásti a data uložená na ně. Zahrnuje také seznam dotazů, které by mohly mít o zabezpečení Microsoft Azure StorSimple a odpovídající odpovědi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrana služby StorSimple Manager zařízení

Service Manager zařízení StorSimple je služba správy hostované ve službě Microsoft Azure a použít ke správě všech zařízení StorSimple, které vaše organizace zprostředkoval. Mají přístup ke službě StorSimple Manager zařízení pomocí firemní přihlašovací údaje pro přihlášení k portálu Azure prostřednictvím webového prohlížeče.

Přístup ke službě StorSimple Manager zařízení vyžaduje, že vaše organizace používat předplatné Azure, která zahrnuje StorSimple. Vaše předplatné řídí funkce, které můžete přístup k portálu Azure. Pokud vaše organizace nemá předplatné služby Azure a chcete další informace o nich najdete v tématu [registraci do Azure jako organizace](../active-directory/sign-up-organization.md).

Vzhledem k tomu, že služba Správce zařízení StorSimple je hostována v Azure, je chráněn funkce zabezpečení Azure. Další informace o funkcích zabezpečení poskytované Microsoft Azure, přejděte na [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrana zařízení StorSimple

Zařízení StorSimple je místní hybridní úložné zařízení, která obsahuje jednotek SSD (Solid-State Drive) a pevných disků (HDD), společně s redundantní řadiče a možnosti automatické převzetí služeb při selhání. V řadičích spravovat úložiště vrstvení, umístění aktuálně používá (nebo aktivní) data na místní úložiště (v zařízení StorSimple zařízení nebo na místní servery), při přesouvání méně často používaná data do cloudu.

Pouze oprávnění StorSimple zařízení jsou povoleny pro připojení služby StorSimple Manager zařízení, který jste vytvořili ve vašem předplatném Azure. Autorizace zařízení, je nutné zaregistrovat u služby StorSimple Manager zařízení tím, že poskytuje registrační klíč služby. Registrační klíč služby je 128-bit náhodný klíč vygenerovaný na portálu Azure.

![Registrační klíč služby](./media/storsimple-security/ServiceRegistrationKey.png)

Další informace jak získat registrační klíč služby, přejděte na [krok 2: získání registračního klíče služby](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Registrační klíč služby je dlouhá klíč, který obsahuje 100 + znaky. Můžete zkopírovat klíč a uložit ho do textového souboru v zabezpečeném umístění, aby ho můžete používat k autorizaci další zařízení podle potřeby. Pokud po registraci zařízení první dojde ke ztrátě registrační klíč služby, můžete vygenerovat nový klíč ze služby StorSimple Manager zařízení. To nebude mít vliv na provoz existující zařízení.

Po registraci zařízení používá tokeny ke komunikaci s Microsoft Azure. Po registraci zařízení se nepoužije registrační klíč služby.

> [!NOTE]
> Doporučujeme, abyste po každé použití znovu vygenerovat registrační klíč služby.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Ochrana vašeho řešení StorSimple pomocí hesla

Hesla jsou důležitým aspektem zabezpečení počítače a jsou používány v řešení StorSimple k zajištění, že vaše data jsou přístupné jenom oprávněným uživatelům. StorSimple můžete konfigurovat následující hesla:

* Heslo správce zařízení StorSimple
* Výzvu, hesla a iniciátor ověřování protokol CHAP (Handshake)
* Heslo správce Snapshot Manageru zařízení StorSimple

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Prostředí Windows PowerShell pro zařízení StorSimple a heslo správce zařízení StorSimple

Prostředí Windows PowerShell pro StorSimple je rozhraní příkazového řádku, který můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple je funkce, které vám umožní zaregistrovat zařízení, konfigurace síťového rozhraní na vašem zařízení, instalace určité typy aktualizací, řešení potíží s zařízení s přístupem k relaci podpory a změnit stav zařízení. Prostředí Windows PowerShell pro StorSimple můžete přistupovat pomocí připojení ke konzole sériového portu v zařízení nebo pomocí vzdálené komunikace Windows Powershellu.

Vzdálená komunikace prostředí PowerShell lze provést prostřednictvím protokolu HTTPS nebo HTTP. Pokud je povoleno vzdálené správy přes protokol HTTPS, musíte stáhnout certifikát pro vzdálenou správu ze zařízení a nainstalujte ji na vzdáleném klientovi. Další informace o vzdálenou komunikaci prostředí PowerShell, přejděte na [připojit vzdáleně k zařízení StorSimple](storsimple-8000-remote-connect.md).

Po připojení k zařízení pomocí Windows Powershellu pro StorSimple, musíte se k zadání hesla správce zařízení pro přihlášení k zařízení.

![Heslo správce zařízení](./media/storsimple-security/DeviceAdminPW.png)

Následující osvědčené postupy mějte na paměti:

* Vzdálená správa je ve výchozím nastavení vypnutý. Můžete povolit službu StorSimple Manager zařízení. Jako osvědčený postup zabezpečení by měl být povolen vzdálený přístup pouze během časového období, která je skutečně potřeba.
* Pokud změníte heslo, ujistěte se, že jste oznámit všichni uživatelé vzdáleného přístupu tak, že nedochází ztrátě neočekávané připojení.
* Služby StorSimple Manager zařízení nelze načíst existující hesla: ho můžete pouze resetovat. Doporučujeme uložit všechna hesla na bezpečném místě, takže není nutné resetovat heslo, pokud je zapomenete. Pokud potřebujete k resetování hesla, ujistěte se, že jste upozornit všechny uživatele, než ho resetovat.

Rozhraní Windows PowerShell můžete přistupovat pomocí sériové připojení k zařízení. Můžete také k němu přístup vzdáleně pomocí protokolu HTTP nebo HTTPS, který zvýšit zabezpečení. Protokol HTTPS nabízí vyšší úroveň zabezpečení než buď sériové nebo připojení HTTP. Ale pro použití protokolu HTTPS, musíte nejdřív nainstalujete certifikát na klientském počítači, který bude přistupovat k zařízení. Certifikát pro vzdálený přístup můžete stáhnout ze stránky konfigurace zařízení ve službě StorSimple Manager zařízení. Pokud dojde ke ztrátě certifikát pro vzdálený přístup, je nutné stáhnout nový certifikát a rozšíří na všechny klienty, kteří mají oprávnění využívat vzdálenou správu.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Výzvu, hesla a iniciátor ověřování protokol CHAP (Handshake)

Protokol CHAP je příslušné schéma ověřování používané zařízení StorSimple pro ověření identity vzdálených klientů. Ověření je založena na sdílené heslo. CHAP může být jednosměrná (Jednosměrný) nebo vzájemné (obousměrné). Cíl (zařízení StorSimple) s jednosměrný CHAP, ověřuje iniciátor (hostitel). Vzájemné nebo zpětného CHAP vyžaduje, aby cíl ověření iniciátoru a pak iniciátoru ověření cíl. Vaše zařízení StorSimple lze nakonfigurovat k využívání těchto metod.

Když konfigurujete CHAP být pamatovat na následující:

* Uživatelské jméno CHAP musí obsahovat méně než 233 znaků.
* CHAP heslo musí být 12 až 16 znaků. Probíhá pokus o použít delší uživatelské jméno nebo heslo bude mít za následek chybu ověřování na hostiteli systému Windows.
* Pro iniciátoru CHAP a cíle CHAP nelze použít stejné heslo.
* Jakmile nastavíte heslo, můžete změnit ale ho nelze načíst. Pokud se změní heslo, ujistěte se, že jste oznámit všichni uživatelé vzdáleného přístupu tak, aby mohli úspěšně připojit k zařízení StorSimple.

Další informace o CHAP a jak ho nakonfigurovat pro vašeho řešení StorSimple, přejděte na [konfigurace CHAP pro vaše zařízení StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Heslo správce Snapshot Manageru zařízení StorSimple

Snapshot Manager zařízení StorSimple je modul snap-in konzoly Microsoft Management Console (MMC), které používá svazek skupin a služby Stínová kopie svazku systému Windows ke generování zálohování konzistentní s aplikací. Kromě toho můžete Snapshot Manager zařízení StorSimple vytvořit plánů zálohování a klonování nebo obnovit svazky.

Při konfiguraci zařízení pro použití Snapshot Manager zařízení StorSimple, bude se budete muset zadat heslo Snapshot Manager zařízení StorSimple. Toto heslo je nejdřív nastavit ve Windows Powershellu pro StorSimple během registrace. Heslo můžete také nastavit a změnit ze služby StorSimple Manager zařízení. Toto heslo ověřuje zařízení s StorSimple Snapshot Manager.

![Heslo správce Snapshot Manageru zařízení StorSimple](./media/storsimple-security/SnapshotMgrPassword.png)

Heslo Snapshot Manager zařízení StorSimple musí být 14 až 15 znaků a musí obsahovat 3 nebo více z kombinace velká písmena, malá písmena, číselné a speciální znaky. Po nastavení hesla Snapshot Manageru zařízení StorSimple, lze změnit, ale nemohou být načteni. Pokud změníte heslo, ujistěte se, že jste oznámit všichni vzdálení uživatelé.

Další informace o Snapshot Manager zařízení StorSimple, přejděte na [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Osvědčené postupy heslo

Doporučujeme, abyste pomocí následujících pokynů k zajištění, že jsou hesel zařízení StorSimple silné a dobře chráněný:

* Změna hesla každé tři měsíce. Změna hesla se vynucuje ročně.
* Používejte silná hesla. Další informace, přejděte na [vytvářet silnější hesla a chránit je](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Vždy používejte různá hesla pro jiný přístup mechanismy; Každý z hesla, které zadáte, musí být jedinečné.
* Nemáte sdílet hesla s každý, kdo nemá oprávnění k přístupu k zařízení StorSimple.
* Mluvit o zadání hesla před ostatní nebo pomocného parametru na formát hesla.
* Pokud máte podezření, že účtu nebo hesla došlo k ohrožení zabezpečení, Nahlaste incident oddělení pro zabezpečení informací.
* Všechna hesla považovat za důvěrné, citlivé informace. 

## <a name="storsimple-data-protection"></a>Ochrana dat StorSimple

Tato část popisuje funkce zabezpečení zařízení StorSimple, které chrání data při přenosu a uložená data.

Jak je popsáno v dalších částech, hesla se používají k autorizaci a ověřování uživatelů, než získají přístup k řešení StorSimple. Zabezpečení zamyslet je ochrana dat před neoprávněnými uživateli, zatímco probíhá přenosu mezi úložných systémů, a když je uložená. Následující části popisují funkce ochrany dat, který je součástí StorSimple.

> [!NOTE]
> Odstranění duplicitních dat zajišťuje zvýšenou ochranu pro data uložená na zařízení StorSimple a v úložišti Microsoft Azure. Když se odstranění duplicit dat, datové objekty, které se ukládají odděleně od metadata, používá k mapování a přistupovat k nim: neexistuje žádný dostupný kontext úroveň úložiště k rekonstrukci dat na základě strukturu svazku, systém souborů nebo název souboru.


## <a name="protect-data-flowing-through-the-service"></a>Ochrana dat předávaných mezi službu

Primárním účelem služby StorSimple Manager zařízení je spravovat a nakonfigurovat zařízení StorSimple. Spustí službu StorSimple Manager zařízení v Microsoft Azure. Zadejte data konfigurace zařízení pomocí portálu Azure a použije službu StorSimple Manager zařízení k odesílání dat do zařízení v Microsoft Azure. StorSimple používá systém asymetrických dvojic klíčů k zajištění, že ohrožení služby Azure nebudou mít za následek ohrožení uložené informace.

![Šifrování dat v cestě](./media/storsimple-security/DataEncryption.png)

Asymetrické klíče systému pomáhá chránit data, která toků prostřednictvím služby následujícím způsobem:

1. Certifikát pro šifrování dat, který používá asymetrické veřejné a privátní klíč pár se generuje na zařízení a slouží k ochraně dat. Klíče jsou generovány, pokud je první zařízení zaregistrované.
2. Certifikát šifrovací klíče dat jsou exportovány do souboru Personal Information Exchange (.pfx), který je chráněný pomocí šifrovacího klíče dat služby, což je silné 128 bitů klíč, který je náhodně generované první zařízení během registrace.
3. Veřejný klíč certifikátu se bezpečně zpřístupněn pro službu StorSimple Manager zařízení a privátní klíč zůstane se zařízením.
4. Data zadávání službu šifrovaná pomocí veřejného klíče a dešifrována pomocí privátní klíč uložený na zařízení, zajistíte, že služba Azure nelze dešifrovat data předávaných do zařízení.

Šifrovací klíč dat služby se vygeneruje pouze první zařízení zaregistrovaná ve službě service. Všechna další zařízení, která jsou zaregistrovaná ve službě service musí používat stejný šifrovací klíč dat služby.

> [!IMPORTANT]
> Je velmi důležité vytvořit kopii šifrovacího klíče dat služby a uložit na bezpečném místě. Kopie šifrovacího klíče dat služby by měly být uložené tak, aby byla přístupná pomocí oprávněné osoby a můžete snadno přenést do Správce zařízení.
> 
> Pokud dojde ke ztrátě šifrovacího klíče dat služby, pracovníci technické podpory společnosti Microsoft můžete ji načíst za předpokladu, že máte alespoň jedno zařízení ve stavu online. Doporučujeme vám, že po načtením změníte šifrovacího klíče dat služby.

Chcete-li změnit šifrovacího klíče dat služby a odpovídající certifikát šifrování dat, postupujte podle kroků v [změnit šifrovacího klíče dat služby pro služby StorSimple Manager zařízení](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Změna šifrovací klíče vyžaduje, aby všechna zařízení aktualizovat pomocí nového klíče. Doporučujeme proto, že změníte klíč všechna zařízení jsou online. Pokud zařízení offline, jejich klíče lze změnit v jinou dobu. Zařízení s zastaralé klíče bude nadále možné spustit zálohování, ale nebudou schopni obnovit data, až po aktualizaci klíče.

Šifrovací klíč dat služby a certifikát datového šifrovacího nevyprší platnost. Doporučujeme však změnit šifrovacího klíče dat služby každý rok, aby se zabránilo ohrožení zabezpečení klíčů.

## <a name="protect-data-at-rest"></a>Ochrana dat v klidovém stavu

Zařízení StorSimple spravuje data jejich uložením do vrstvy místně a v cloudu, v závislosti na četnost použití. Všechny hostitele počítače, které jsou připojené k zařízení posílat data do zařízení, což pak přesune data do cloudu, podle potřeby. Data se přenáší ze zařízení do cloudu bezpečně prostřednictvím Internetu. Každé zařízení má jeden cíl iSCSI, která vyvolá všechny sdílené svazky na daném zařízení. Všechna data zašifrována před zasláním do cloudového úložiště. 

![Šifrovací klíč cloudového úložiště](./media/storsimple-security/CloudStorageEncryption.png)

K zajištění zabezpečení a integrity dat přesunout do cloudu, StorSimple umožňuje definovat cloudové úložiště šifrovacích klíčů následujícím způsobem:

* Když vytvoříte kontejner svazků zadáte šifrovací klíč cloudového úložiště. Klíč nelze změnit ani přidat později.
* Všechny svazky v kontejneru svazků sdílet stejný šifrovací klíč. Pokud chcete různé typy šifrování pro konkrétní svazek, doporučujeme vytvořit nový kontejner svazek k hostování tohoto svazku.
* Když zadáte šifrovací klíč cloudového úložiště ve službě Správce zařízení StorSimple, že je klíč zašifrovaný pomocí veřejnou část šifrovacího klíče dat služby a pak se odešle do zařízení.
* Šifrovací klíč cloudového úložiště není uložit kdekoli v rámci služby a znáte jenom na zařízení.
* Určení šifrovací klíč cloudového úložiště je volitelné. Může odesílat data, která byla dříve zašifrována na hostitele do zařízení.

### <a name="additional-security-best-practices"></a>Osvědčené postupy zabezpečení další

* Rozdělit provoz: nasazení zcela oddělené sítě a pomocí sítí VLAN, kde fyzické izolace není možné izolovat vaše síť SAN iSCSI z provozu generovaného uživateli v podnikové síti LAN. Síť vyhrazený pro úložiště iSCSI bude zajistit bezpečnost a výkon vaše důležitá obchodní data. Kombinování přenosů dat úložiště a uživatelů v podnikových sítích LAN se nedoporučuje a můžete zvýšit latence a způsobit selhání sítě.
* Pro zabezpečení sítě na straně hostitele použijte síťových rozhraní, které podporují TCP/IP Offload Engine (TOE). TOE snižuje zatížení procesoru zpracování protokolu TCP v síťovém adaptéru.

## <a name="protect-data-via-storage-accounts"></a>Ochrana dat pomocí účtů úložiště

Každé předplatné Microsoft Azure můžete vytvořit jeden nebo více účtů úložiště. (Účet úložiště poskytuje jedinečný obor názvů pro práci s daty uloženými v cloudu Azure.) Přístup k účtu úložiště se řídí předplatného a přístupových klíčů, které jsou přidružené k tomuto účtu úložiště.

Při vytváření účtu úložiště vygeneruje Microsoft Azure dva 512bitové přístupové klíče k úložišti, z nichž jeden slouží k ověřování, když zařízení StorSimple získá přístup k účtu úložiště. Všimněte si, že pouze jeden z těchto klíčů se používá. Další klíč je uchovávat v rezervy, abyste mohli klíče pravidelně otočit. Otočit klíče, takže aktivní sekundární klíč a pak odstraňte primární klíč. Potom můžete vytvořit nový klíč pro použití při další otočení. (Z bezpečnostních důvodů vyžadují mnoho datových centrech střídání klíčů.)

Doporučujeme, postupujte podle těchto osvědčené postupy pro střídání klíčů:

* Klíče účtu úložiště pravidelně k zajištění, že váš účet úložiště není přístup neoprávnění uživatelé by měl otočit.
* Správce Azure měli pravidelně, změnit nebo znovu vygenerovat primární nebo sekundární klíč pomocí úložiště části portálu Azure přímý přístup k účtu úložiště.

## <a name="protect-data-via-encryption"></a>Chránit data pomocí šifrování

StorSimple používá tyto algoritmy šifrování chránit data uložená v nebo cestě mezi součástmi vašeho řešení StorSimple.

| Algoritmus | Délka klíče | Protokoly/aplikace/komentáře |
| --- | --- | --- |
| RSA |2048 |V1.5 RSA PKCS č. 1 se používá na portálu Azure k šifrování dat konfigurace, která se odešle do zařízení: například přihlašovací údaje, konfigurace zařízení StorSimple, účtu úložiště a cloudové úložiště šifrovacích klíčů. |
| AES |256 |AES s CBC se používá k šifrování veřejnou část šifrovacího klíče dat služby před odesláním do portálu Azure ze zařízení StorSimple. Také se používá zařízení StorSimple k šifrování dat před odesláním dat účet cloudového úložiště. |

## <a name="storsimple-cloud-appliance-security"></a>Zabezpečení cloudu zařízení StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Následují některé otázky a odpovědi týkající se zabezpečení a Microsoft Azure StorSimple.

**Otázka:** mé služby dojde k ohrožení bezpečnosti. Moje další kroky, které mají být?

**Odpověď:** by měl hned změnit šifrovacího klíče dat služby a klíče účtu úložiště pro účet úložiště, který se používá pro vrstvení data. Pokyny najdete v tématu:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Střídání klíče účtů úložiště](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Otázka:** mám nové zařízení StorSimple, které se žádostí o registrační klíč služby. Jak jej lze vyhledat?

**Odpověď:** tento klíč byl vytvořen při prvním vytvoření služby StorSimple Manager zařízení. Pokud používáte službu StorSimple Manager zařízení pro připojení k zařízení, můžete na stránce Rychlý start k zobrazení nebo znovu vygenerovat registrační klíč služby. Generování nový registrační klíč služby nebude mít vliv na stávající registrovaná zařízení. Pokyny najdete v tématu:

* [Zobrazení nebo znovu vygenerovat registrační klíč služby](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**Otázka:** ztrátou Moje šifrovacího klíče dat služby. Co mám udělat?

**Odpověď:** obraťte se na podporu společnosti Microsoft. Se může přihlásit k relaci podpory na zařízení a nápovědy načíst klíč (za předpokladu, že je online alespoň jedno zařízení). Ihned po získáte šifrovacího klíče dat služby, měli byste ji zajistit, že se nový klíč označuje pouze pro vás změnit. Pokyny najdete v tématu:

* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**Otázka:** I oprávnění zařízení pro změnu klíče šifrování dat služby, ale proces změny klíče se nespustila. Co bych měl/a dělat?

**Odpověď:** Pokud vypršela platnost časový limit, budete muset opětovné pověření zařízení pro změnu klíče šifrování dat služby a znovu spustit proces.

**Otázka:** po změně šifrovacího klíče dat služby, ale I se nepodařilo aktualizovat jiných zařízení v rámci 4 hodiny. Je nutné znovu spustit?

**Odpověď:** 4 hodin časové období se pouze za inicializaci změnu. Po spuštění procesu aktualizace na autorizované zařízení StorSimple, autorizaci je platný, dokud nejsou aktualizovány všechna zařízení.

**Otázka:** naše StorSimple správce opustil společnost. Co bych měl/a dělat?

**Odpověď:** změny a resetování hesel, která umožňují přístup k zařízení StorSimple a změnit šifrování dat služby klíče zajistit, že nové informace o nezná Neautorizováno pracovníky. Pokyny najdete v tématu:

* [Chcete-li změnit hesla služby storsimple používat službu Správce zařízení StorSimple](storsimple-8000-change-passwords.md)
* [Změna šifrovacího klíče dat služby](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurace protokolů CHAP pro vaše zařízení StorSimple](storsimple-8000-configure-chap.md)

**Otázka:** chcete zadejte heslo Snapshot Manager zařízení StorSimple na hostitele, který se připojuje k zařízení StorSimple, ale heslo není k dispozici. Co můžu udělat?

**Odpověď:** Pokud jste zapomněli heslo, měli byste vytvořit nový. Potom nezapomeňte informovat všechny existující uživatele změnil heslo a že budou by měl aktualizovat jejich klientům používat nové heslo. Pokyny najdete v tématu:

* [Změňte heslo Snapshot Manager zařízení StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Ověřování zařízení](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Otázka:** certifikát pro vzdálený přístup k prostředí Windows PowerShell pro StorSimple se změnil na zařízení. Jak aktualizovat Moje klienty vzdáleného přístupu?

**Odpověď:** můžete stáhnout nového certifikátu ze služby StorSimple Manager zařízení a pak zadejte, aby byl nainstalován v úložišti certifikátů klientů vzdáleného přístupu. Pokyny najdete v tématu:

* [Rutina Import certifikátu](https://technet.microsoft.com/library/hh848630.aspx)

**Otázka:** je můj data chráněná, pokud dojde k narušení služby StorSimple Manager zařízení?

**Odpověď:** konfiguračních dat služby je vždy šifrované svým veřejným klíčem, při zobrazení ve webovém prohlížeči. Protože služba nemá přístup k privátnímu klíči, služba nebude moci zobrazit žádná data. Pokud dojde k narušení služby StorSimple Manager zařízení, neexistuje žádný vliv, protože neexistují žádné klíčů uložených ve službě StorSimple Manager zařízení.

**Otázka:** Pokud někdo získá přístup k certifikátu šifrování dat, budou data dojít k ohrožení?

**Odpověď:** Microsoft Azure ukládá zákazníka datový šifrovací klíč (soubor .pfx) v zašifrovaném formátu. Protože je šifrovaný soubor .pfx a služba StorSimple nemá šifrovacího klíče dat služby pro dešifrování souboru .pfx, jednoduše získávání přístupu k souboru .pfx nebude vystavit všech tajných klíčů.

**Otázka:** co se stane, když vládních entity Microsoft požádá o svá data?

**Odpověď:** vzhledem k tomu, že všechna data se šifrují službu a privátní klíč se uchovává se zařízením, vládních entity musí zákazník zeptat data.

## <a name="gdpr-compliance"></a>GDPR dodržování předpisů

[Obecné Data Protection nařízení (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) je data protection a o ochraně osobních údajů zákon Evropské unie (EU). GDPR obsahuje mnoho požadavky na to, jak shromažďování, ukládání a používat osobní údaje. Pravidla GDPR jsou vynucená pro společnosti, vládních organizací a dalšími organizacemi, které fungují v Evropa a shromažďovat, analyzovat data svázané s obyvatele Evropské unie. 

Správce zařízení StorSimple řady, fyzických i virtuálních je GDPR kompatibilní. Existují dva klíče instance, kde je osobní údaje shromážděné a zobrazí v rámci služby:

- Výstraha uživatelská nastavení, které jsou nakonfigurované e-mailovou adresu uživatele. Tyto informace můžete zobrazit a vymazat správcem. To platí pro řadu zařízení StorSimple 8000 i pole virtuální zařízení StorSimple.
 * K zobrazení a vymazat nastavení pro řady StorSimple 8000, postupujte podle kroků v [zobrazení a Správa výstrah StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
 * K zobrazení a vymazat nastavení pro pole virtuální zařízení StorSimple, postupujte podle kroků v [zobrazení a Správa výstrah StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Uživatelé, kteří měli přístup k datům, které se nacházejí na sdílené složky. Zobrazí se seznam uživatelů, kteří měli přístup k datům sdílené složky a lze zobrazit. Tento seznam je taky odstranit po odstranění sdílené složky. Vztahuje se pouze na pole virtuální zařízení StorSimple.
 * Chcete-li zobrazit seznam uživatele, který lze zobrazit nebo odstranit sdílenou složku, postupujte podle kroků v [spravovat sdílené složky v poli virtuální zařízení StorSimple](storsimple-virtual-array-manage-shares.md)

Další informace najdete v tématu Zásady Microsoft Privacy v [Centrum zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

[Nasazení zařízení StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

