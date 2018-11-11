---
title: Osvědčené postupy pro StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje osvědčené postupy pro nasazení a správu StorSimple Virtual Array.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2018
ms.author: alkohli
ms.openlocfilehash: b8e9f12a549f71971c2da3b9865f6a74dad58f61
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300910"
---
# <a name="storsimple-virtual-array-best-practices"></a>Osvědčené postupy pro StorSimple Virtual Array
## <a name="overview"></a>Přehled
Microsoft Azure StorSimple Virtual Array je integrované řešení úložiště, které spravuje úlohy úložiště mezi virtuální zařízení s místní spuštění v hypervisoru a cloudovým úložištěm Microsoft Azure. StorSimple Virtual Array je efektivní a nákladově efektivní alternativu k poli fyzické řady 8000. Virtuální pole můžete spustit na vaší stávající infrastruktury hypervisoru, podporuje iSCSI a protokoly SMB a je velmi vhodná pro scénáře, vzdálené kanceláře či firemní office. Další informace o řešení StorSimple, přejděte na [Přehled služby Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Tento článek se zabývá osvědčenými postupy, které jsou implementované během počáteční instalace, nasazování a správu StorSimple Virtual Array. Tyto osvědčené postupy poskytují ověřené pokyny pro nastavení a správu vaše virtuální pole. Tento článek je zaměřena na správce IT, kteří nasadit a spravovat virtuální pole určená ve svých datacentrech.

Doporučujeme, abyste pravidelné kontroly osvědčených postupů, které pomáhají zajistit, aby že vaše zařízení je stále v dodržování předpisů při změně instalační nebo provozní tok. By měl mít nějaké problémy při implementaci těchto osvědčených postupů na vaše virtuální pole [obraťte se na Microsoft Support](storsimple-virtual-array-log-support-ticket.md) žádostí o pomoc.

## <a name="configuration-best-practices"></a>Osvědčenými postupy konfigurace
Tyto osvědčené postupy pokrývají pokyny, které je potřeba dodržet během počáteční instalace a nasazení virtuálních polí. Zahrnout tyto osvědčené postupy související se zřizování virtuálního počítače, nastavení zásad skupiny, změny velikosti, nastavení sítě, konfiguraci účtů úložiště a vytvoření sdílených složek a svazků pro virtuální pole. 

### <a name="provisioning"></a>Zřizování
StorSimple Virtual Array se virtuální počítač (VM) zřízený v hypervisoru (Hyper-V nebo VMware) serveru hostitele. Při zřizování virtuálního počítače, ujistěte se, že se váš hostitel věnovaly dostatek prostředků. Další informace najdete v části [minimální požadavky na zdroje](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) ke zřízení pole.

Implementujte následující osvědčené postupy při zřizování virtuálního pole:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ virtuálního počítače** |**2. generace** virtuálního počítače pro použití s Windows serverem 2012 nebo novější a *.vhdx* bitové kopie. <br></br> **1. generace** virtuálního počítače pro použití s Windows serverem 2008 nebo novější a *VHD* bitové kopie. |Verze virtuálního počítače použijte 8 při použití *.vmdk* bitové kopie. |
| **Typ paměti** |Nakonfigurovat jako **statickou paměť**. <br></br> Nepoužívejte **dynamické paměti** možnost. | |
| **Datový typ disku** |Zřízení jako **dynamicky se zvětšující**.<br></br> **Pevná velikost** trvá moc dlouho. <br></br> Nepoužívejte **rozdílové** možnost. |Použití **dynamicky zřizovat** možnost. |
| **Úprava dat disku** |Rozšíření nebo zmenšení není povolený. Pokus o Uděláte to tak vede ke ztrátě všech místních dat na zařízení. |Rozšíření nebo zmenšení není povolený. Pokus o Uděláte to tak vede ke ztrátě všech místních dat na zařízení. |

### <a name="sizing"></a>Velikost
Při nastavování velikosti StorSimple Virtual Array, vezměte v úvahu následující faktory:

* Místní rezervace pro svazky nebo sdílené složky. Přibližně 12 % prostor je vyhrazeno u místní vrstvy pro každý zřízené vrstvený svazek nebo sdílenou složku. Přibližně 10 % prostoru je také rezervovaná pro místně vázaný svazek systému souborů.
* Režie snímku. Přibližně 15 % místa na místní úrovni je vyhrazený pro snímky.
* Je nutný pro obnovení. Pokud jako novou operaci obnovení, určení velikosti by měl účet pro prostor potřebný pro obnovení. Obnovení provádí do sdílené složky nebo svazku stejné velikosti.
* Některé vyrovnávací paměti by měl být přiděleno pro jakékoli neočekávaném růstu.

Založené na předchozích faktorech, požadavky na velikost může být reprezentován do následující rovnice:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Následující příklady znázorňují, jak můžete měnit velikost virtuálního pole na základě vašich požadavků.

#### <a name="example-1"></a>Příklad 1:
Na vaše virtuální pole chcete být schopni

* zřízení 2 TB vrstvený svazek nebo sdílenou složku.
* zřízení 1 TB vrstvený svazek nebo sdílenou složku.
* zřízení 300 GB místně vázaný svazek nebo sdílenou složku.

Pro předchozí svazky nebo sdílené složky dejte nám vypočítejte požadavky na místo na místní úrovni.

Nejprve pro každý vrstvený svazek nebo sdílenou složku, místní rezervace by být roven 12 % velikosti svazku nebo sdílené složky. Místní rezervace pro místně vázaný svazek nebo sdílenou složku, je 10 % velikosti místně vázaný svazek nebo sdílenou složku (kromě zřízená velikost). V tomto příkladu budete potřebovat

* Místní rezervace 240 GB (pro 2 TB vrstvený svazek nebo sdílenou složku)
* Místní rezervace 120 GB (pro 1 TB vrstvený svazek nebo sdílenou složku)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (zvýšení 10 % místní rezervace velikost 300 GB zřízený)

Celkové požadované místo na u místní vrstvy zatím je: 240 GB + 120 GB + 330 GB = 690 GB.

Za druhé potřebujeme alespoň tolik místa na místní úrovni jako největší jednu rezervaci. Tuto další dobu se používá v případě, že potřebujete obnovit ze snímku cloudu. V tomto příkladu je největší místní rezervace 330 GB (včetně rezervace pro systém souborů), takže přidejte, že do těchto 690 GB: 690 GB + 330 GB = 1020 GB.
Pokud jsme provedli následující další obnovení, jsme vždy uvolněte místo z předchozí operace obnovení.

Pak potřebujeme 15 % této vaše celkové volné místo zatím k uložení místní snímky, tak, aby byla k dispozici pouze 85 %. V tomto příkladu by kolem 1020 GB = než 0,85&ast;zřízené datový disk TB. Proto, by se zřízené datový disk (1020&ast;(1/než 0,85)) = 1 200 GB = 1,20 TB ~ 1,25 TB (zaokrouhlení na nejbližší QUARTIL)

Které budou zohledňovat v neočekávaném růstu a nové obnovení, měli byste zřídit místní disk kolem 1,25 – 1,5 TB.

> [!NOTE]
> Doporučujeme také, že je místní disk tence zřízený. Toto doporučení se totiž místo obnovení je potřeba, pouze pokud chcete obnovit data, která je starší než pět dní. Obnovení na úrovni položky můžete obnovit data za posledních pěti dní bez nutnosti přebytečné místo pro obnovení.


#### <a name="example-2"></a>Příklad 2:
Na vaše virtuální pole chcete být schopni

* zřízení vrstvený svazek 2 TB
* Zřiďte místně připojený svazek 300 GB

Podle % 12 rezervace místní prostor pro vrstvené svazky nebo sdílené složky a 10 % pro místně připojené svazky nebo sdílené složky, potřebujeme

* Místní rezervace 240 GB (2 TB vrstvený svazek nebo sdílenou složku)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (zvýšení 10 % místní rezervace 300 GB zřízené místo)

Celkové požadované místo na disku u místní vrstvy je: 240 GB + 330 GB = 570 GB

Minimální volné místo potřebné pro obnovení je 330 GB.

15 %, celkové disku se používá pro ukládání snímků tak, aby byla k dispozici pouze než 0,85. Proto je velikost disku (900&ast;(1/než 0,85)) = 1.06 TB ~ 1,25 TB (zaokrouhlení na nejbližší QUARTIL)

Které budou zohledňovat nárůst žádné neočekávané, můžete zřídit na místní disk 1,25 – 1,5 TB.

### <a name="group-policy"></a>Zásady skupiny
Zásady skupiny jsou infrastruktura, která umožňuje implementovat konkrétní konfigurace pro uživatele a počítače. Nastavení zásad skupiny jsou obsaženy v objektech zásad skupiny (GPO), které jsou propojeny s následujících kontejnerech služby Active Directory Domain Services (AD DS): weby, domény a organizační jednotky (OU). 

Pokud vaše virtuální pole je připojený k doméně, objekty zásad skupiny můžete použít k němu. Tyto objekty zásad skupiny můžete nainstalovat aplikace jako antivirový software, který může nepříznivě ovlivnit fungování StorSimple Virtual Array.

Proto doporučujeme, aby vás:

* Ujistěte se, že vaše virtuální pole je ve vlastní organizační jednotka (OU) služby Active Directory.
* Ujistěte se, že žádné objekty zásad skupiny (GPO) se použijí pro vaše virtuální pole. Můžete zablokovat dědičnost Ujistěte se, že virtuální pole (podřízený uzel) nedědí automaticky žádnými objekty zásad skupiny z nadřazeného objektu. Další informace najdete v části [zablokovat dědičnost](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Sítě
Konfigurace sítě pro vaše virtuální pole se provádí prostřednictvím místního webového uživatelského rozhraní. Virtuální síťové rozhraní je povolená díky hypervisoru, ve kterém je virtuální pole zřízené. Použití [nastavení sítě](storsimple-virtual-array-deploy3-fs-setup.md) stránku konfigurace IP adresy rozhraní virtuální sítě, podsítě a bránu.  Můžete také nakonfigurovat primární a sekundární server DNS, nastavení času a volitelná nastavení proxy serveru pro vaše zařízení. Většina konfigurace sítě se o jednorázové nastavení. Zkontrolujte [StorSimple požadavky na síť](storsimple-ova-system-requirements.md#networking-requirements) před nasazením virtuálního pole.

Když nasazujete virtuální pole, doporučujeme dodržovat tyto osvědčené postupy:

* Zajistěte, aby sítě, ve kterém je virtuální pole vždy nasazený kapacity vyhradit 5 MB/s šířky pásma Internetu (nebo více).
  
  * V závislosti na charakteristikách vašich úloh a mírou změn dat se liší potřeby šířky pásma Internetu.
  * Změna dat, která může být zpracována je přímo úměrná internetové šířce pásma. Například při vytvoření zálohy 5 MB/s šířky pásma zvládne přibližně 18 GB Změna dat v 8 hodin. Čtyřikrát větší šířka pásma (20 MB/s) může zpracovávat čtyřnásobnou změn dat (72 GB).
* Ujistěte se, že budou vždy k dispozici připojení k Internetu. Připojení k Internetu výskyt občasný, nebo nespolehlivé na zařízení může mít za následek ztrátu přístupu k datům v cloudu a může mít za následek má nepodporovanou konfiguraci.
* Pokud plánujete nasazení zařízení jako serveru iSCSI:
  
  * Doporučujeme zakázat **získat IP adresu automaticky** možnost (DHCP).
  * Konfigurace statických IP adres. Je nutné nakonfigurovat primární a sekundární server DNS.
  * Pokud definujete více síťových rozhraní na vaše virtuální pole, pouze první síťové rozhraní (ve výchozím nastavení, toto rozhraní je **Ethernet**) můžete oslovit cloudu. K řízení typ provozu, můžete vytvořit několik virtuálních síťových rozhraní na vaše virtuální pole (nakonfigurovaný jako iSCSI server) a připojení těchto rozhraní k různým podsítím.
* Omezení šířky pásma cloudu jenom (používané virtuální pole), konfigurace omezení na směrovači nebo bránu firewall. Pokud definujete šířky pásma v vaše hypervisoru, ji budou omezení všech protokolů, jako jsou třeba iSCSI a SMB místo jenom šířku pásma cloudu.
* Zkontrolujte tuto synchronizaci času pro hypervisory je povolená. Pokud vaše virtuální pole pomocí technologie Hyper-V, vyberte ve Správci technologie Hyper-V, přejděte na **nastavení &gt; integrační služby**a ujistěte se, že **synchronizace času** je zaškrtnuté políčko.

### <a name="storage-accounts"></a>Účty úložiště
StorSimple Virtual Array lze přidružit jeden účet úložiště. Tento účet úložiště může být účet automaticky generované úložiště ve stejném předplatném jako služba, účet nebo účet úložiště související s jiným předplatným. Další informace najdete v tématu Jak [spravovat účty úložiště pro vaše virtuální pole](storsimple-virtual-array-manage-storage-accounts.md).

Pomocí následujících doporučení pro účty úložiště přidružené virtuální pole.

* Při propojování více virtuální pole pomocí jednoho účtu úložiště, zohlednit maximální kapacity (64 TB) pro virtuální pole a maximální velikost (500 TB) pro účet úložiště. To omezí počet Plnohodnotná virtuální pole, které může být přidružený účet úložiště na asi 7.
* Při vytváření nového účtu úložiště
  
  * Doporučujeme vám, že jejím vytvoření v oblast co nejblíž koncovým vzdálené kanceláře či firemní office, které se nasadí StorSimple Virtual Array, chcete-li minimalizovat latenci.
  * Berte v úvahu, že nelze přesunout účtu úložiště v různých oblastech. Také nelze přesunout služby napříč předplatnými.
  * Použijte účet úložiště, který implementuje redundance mezi datových centrech. Geograficky redundantní úložiště (GRS), Zónově redundantního úložiště (ZRS) a místně redundantního úložiště (LRS) jsou všechny podporované pro použití se vaše virtuální pole. Další informace o různých typech účtů úložiště, přejděte na [replikace Azure storage](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Sdílené složky a svazky
Pro StorSimple Virtual Array můžete zřídit sdílené složky, když je nakonfigurovaný jako souborový server a svazky. když je nakonfigurovaný jako serveru iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazků se vztahují na velikost a typ nakonfigurované.

#### <a name="volumeshare-size"></a>Velikost svazku nebo sdílené složky
Na vaše virtuální pole můžete zřídit sdílené složky, když je nakonfigurovaný jako souborový server a svazky. když je nakonfigurovaný jako serveru iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazků se vztahují na velikost a typ nakonfigurované. 

Vzít v úvahu následující osvědčené postupy při zajišťování sdílených složek nebo svazků na svém virtuálním zařízení.

* Velikosti souborů, vzhledem k zřízená velikost vrstvenou sdílenou složku může mít vliv na výkon vrstvení. Práce s velkými soubory by mohlo způsobit zmenšení pomalé vrstvy. Při práci s velkými soubory, doporučujeme vám, že je největší soubor menší než velikost sdílené složky % 3.
* Maximálně 16 svazků nebo sdílených složek se dají vytvořit na virtuální pole. Omezení velikosti místně připnuté a vrstvené svazky nebo sdílené složky, odkazují [omezuje StorSimple Virtual Array](storsimple-ova-limits.md).
* Při vytváření svazku, zvážit využití očekávaná data, stejně jako budoucí růst. Svazek nejde zvětšit později.
* Po vytvoření svazku nelze zmenšit velikost svazku na StorSimple.
* Při zápisu do vrstvený svazek na StorSimple, když objem dat dosáhne určité prahové hodnoty (relativní vůči adresáři místního místa vyhrazeného pro svazek), je omezený vstupně-výstupních operací. Pokračování k zápisu do tohoto svazku může zpomalit vstupně-výstupní operace značně. I když můžete zaznamenat na vrstvený svazek nad rámec jeho zřízená kapacita (jsme nezastavovali aktivně uživateli zápis překročil zřízenou kapacitu), se zobrazí oznámení o tom, které můžete mít přetížený. Jakmile se zobrazí výstraha, je nutné použít nápravná opatření například odstranit data na svazku (rozšíření svazku se aktuálně nepodporuje).
* Pro případy použití pro zotavení po havárii počet povolených sdílených složek nebo svazků je 16 a maximální počet sdílených složek nebo svazků, které lze zpracovat paralelní je také 16, počet sdílených složek nebo svazků nemá vliv na vaše RPO a RTO.

#### <a name="volumeshare-type"></a>Typ svazek nebo sdílenou složku
StorSimple podporuje dva typy svazek nebo sdílenou složku na základě využití: místně připnuté a vrstvené. Místně připojené svazky nebo sdílené složky se zřídilo hodně že vrstvené svazky nebo sdílené složky jsou tence zřízený. Místně vázaný svazek nebo sdílenou složku nejde převést na vrstvené nebo *naopak* po vytvoření.

Doporučujeme implementovat následující osvědčené postupy při konfiguraci StorSimple svazky nebo sdílené složky:

* Určete typ svazku podle úloh, které máte v úmyslu nasadit dřív, než vytvoříte svazek. Použijte místně připojené svazky pro úlohy, které vyžadují místní záruky dat (i během výpadku cloudu) a, které vyžadují cloudu s nízkou latencí. Po vytvoření svazku na vaše virtuální pole nejde změnu typu svazku z místně připnuté na vrstvené nebo *naopak*. Jako příklad vytváření místně vázaných svazků při nasazování úloh hostování virtuálních počítačů (VM); nebo úlohy SQL pro úlohy sdílené složky souboru použijte vrstvené svazky.


#### <a name="volume-format"></a>Formát svazku
Po vytvoření svazky zařízení StorSimple na vašem serveru iSCSI, musíte inicializovat, připojení a formátování svazků. Tato operace se provádí na hostiteli připojen k zařízení StorSimple. Následující osvědčené postupy se doporučuje, když připojení a formátování svazků StorSimple hostitele.

* Proveďte rychlé formátování na všechny svazky zařízení StorSimple.
* Při formátování svazek StorSimple, použijte velikost alokační jednotky (AU) o velikosti 64 KB (výchozí hodnota je 4 KB). 64 KB Austrálie je založená na testování provádí interně pro běžné úlohy StorSimple a další úlohy.
* Při použití StorSimple Virtual Array, které jsou nakonfigurované jako serveru iSCSI, nepoužívejte rozložené svazky ani dynamické disky jako tyto svazky nebo disky nejsou podporovány řešení storsimple.

#### <a name="share-access"></a>Přístup ke sdílené složce
Při vytváření sdílené složky na souborovém serveru se vaše virtuální pole, dodržujte následující pokyny:

* Při vytváření sdílené složky, přiřadíte skupinu uživatelů jako sdílené složky správce místo jednoho uživatele.
* Po vytvoření sdílené složky tak, že upravíte sdílených složek pomocí Průzkumníka Windows, mohou spravovat oprávnění systému souborů NTFS.

#### <a name="volume-access"></a>Přístup ke svazku
Při konfiguraci iSCSI svazky na StorSimple Virtual Array, je důležité pro řízení přístupu, kdykoli je to nutné. Pokud chcete zjistit, které hostitelské servery můžete získat přístup ke svazkům, vytvořit a přidružit záznamy řízení přístupu (záznamů Acr) svazky zařízení StorSimple.

Používejte následující osvědčené postupy při konfiguraci záznamů Acr pro svazky zařízení StorSimple:

* Vždy přidružte aspoň jeden ACR svazku.

* Při přiřazování více než jeden ACR svazku, ujistěte se, že svazek není vystavený způsobem, kde můžete současně přistupovat více než jeden neclusterovaného hostitele. Pokud jste přiřadili více záznamů Acr svazku, upozornění se zobrazí ke kontrole konfigurace.

### <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat
StorSimple Virtual Array má funkce pro zabezpečení a šifrování dat, které zajišťují důvěrnost a integrita dat. Při použití těchto funkcí, se doporučuje dodržovat tyto osvědčené postupy: 

* Definujte šifrovací klíč cloudového úložiště pro generování šifrování AES-256, předtím, než se odešlou z vaše virtuální pole do cloudu. Tento klíč není povinný, pokud vaše data zašifrují na začátku. Klíč může generovat a v bezpečí pomocí systémem správy klíčů, jako [službou Azure key vault](../key-vault/key-vault-whatis.md).
* Při konfiguraci účtu úložiště pomocí služby StorSimple Manager, ujistěte se, že povolíte režim SSL, který chcete vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudu.
* Znovu vygenerujete klíče pro účty úložiště (podle přístupu ke službě Azure Storage) pravidelně na účet pro přístup k žádné změny na základě změny seznamu správců.
* Data na vaše virtuální pole je komprimován a s odstraněním duplicitních dat před odesláním do Azure. Nedoporučujeme používat službu role odstranění duplicitních dat na hostiteli s Windows serverem.

## <a name="operational-best-practices"></a>Aplikovatelné nejlepší postupy
Aplikovatelné nejlepší postupy jsou pokyny, které byste měli dodržet, během operace tohoto virtuálního pole nebo každodenní správu. Tyto postupy pokrývají určité úlohy správy například umožňoval vytvářet zálohy, obnovení ze zálohovacího skladu, převzetí služeb při selhání a její deaktivace a odstranění pole, monitorování využití systému a stav a spuštění virů kontroluje na vaše virtuální pole.

### <a name="backups"></a>Zálohování
Data na vaše virtuální pole je zálohovat do cloudu dvě možnosti, výchozí automatizované denní zálohování celého zařízení spouští ve 22:30 nebo prostřednictvím ručního zálohování na vyžádání. Ve výchozím nastavení zařízení automaticky vytvoří denní snímky cloudových všechna data uložená v něm. Další informace najdete v části [zálohování StorSimple Virtual Array](storsimple-virtual-array-backup.md).

Frekvence a uchovávání, které jsou přidružené k výchozí zálohování nejde změnit, ale můžete nakonfigurovat čas, ve kterém jsou spuštěny denní zálohy každý den. Při konfiguraci spuštění pro automatizované zálohování, doporučujeme, aby:

* Naplánujte zálohování pro hodiny mimo špičku. Čas spuštění zálohování nesmí shodovat s mnoha hostitelem vstupně-výstupních operací.
* Spustit ruční zálohování na vyžádání při plánování provedení převzetí služeb při selhání zařízení nebo před časové období údržby, chcete-li chránit data na vaše virtuální pole.

### <a name="restore"></a>Obnovení
Můžete obnovit ze zálohovacího skladu dvěma způsoby: obnovit na jiný svazek nebo sdílenou složku nebo provádět obnovení na úrovni položek, která je (k dispozici pouze na virtuální pole nakonfigurovali jako souborový server). Obnovení na úrovni položky můžete provádět podrobné obnovení souborů a složek z zálohování v cloudu a všech sdílených složek na zařízení StorSimple. Další informace najdete v části [obnovit ze zálohy](storsimple-virtual-array-clone.md).

Když provádíte obnovení, mějte podle následujících pokynů:

* StorSimple Virtual Array nepodporuje obnovení na místě. Může to ale být snadno dosáhnout dvoustupňový proces: Uvolněte na virtuální pole a potom obnovit na jiný svazek nebo sdílenou složku.
* Při obnovování na místním svazku, mějte na paměti obnovení bude dlouhotrvající operace. I když svazek může rychle režimu online, budou data dál HYDRATOVANÝ na pozadí.
* Typ svazku zůstane stejný během procesu obnovení. Vrstvený svazek je obnovit do jiné vrstvený svazek a místně vázaný svazek na jiný místně připnutý svazek.
* Při pokusu o obnovení ze zálohy svazek nebo sdílenou složku, pokud úloha obnovení selže, nastavte cílový svazek nebo sdílenou složku může být stále vytvořené na portálu. Je důležité odstranění tohoto nevyužité cílového svazku nebo sdílené složky na portálu pro minimalizaci budoucí potíží vzniklých tohoto elementu.

### <a name="failover-and-disaster-recovery"></a>Převzetí služeb při selhání a zotavení po havárii
Převzetí služeb zařízení při umožňuje migrovat data z *zdroj* zařízení v datovém centru na jiný *cílové* zařízení umístěné ve stejné nebo jiné zeměpisné umístění. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání Cloudová data pro zdrojové zařízení změny vlastnictví, která cílového zařízení.

Pro StorSimple Virtual Array můžete pouze převezme služby při selhání jiné virtuální pole, spravuje stejnou službu StorSimple Manager. Převzetí služeb při selhání zařízením řady 8000 nebo pole spravovány jiné služby StorSimple Manager (než ten, který pro zdrojové zařízení) není povolená. Další informace o aspektech převzetí služeb při selhání, přejděte na [předpoklady pro převzetí služeb při selhání zařízení](storsimple-virtual-array-failover-dr.md).

Při provádění převzetí přes pro vaše virtuální pole, mějte následující:

* Plánované převzetí služeb při selhání je součástí osvědčeného provést všechny svazky nebo sdílené složky do offline režimu před zahájení převzetí služeb. Postupujte podle pokynů specifické pro operační systém a provádět s nimi svazky nebo sdílené složky offline hostitele první a pak proveďte tyto offline na svém virtuálním zařízení.
* Souboru server zotavení po havárii (DR) doporučujeme, abyste tak, aby oprávnění ke sdíleným složkám se automaticky vyřeší připojíte zařízení cílové ke stejné doméně jako zdroj. V této verzi se podporuje jenom převzetí služeb při selhání na cílové zařízení ve stejné doméně.
* Po úspěšném dokončení zotavení po Havárii, je automaticky odstraní zdrojové zařízení. I když zařízení už nejsou k dispozici, je virtuální počítač, který jste zřídili v hostitelském systému nadále spotřebovávají prostředky. Doporučujeme vám odstranit tento virtuální počítač z hostitele systému zabránit veškeré poplatky účtují.
* Všimněte si, že i v případě, převzetí služeb při selhání není úspěšné, **data jsou vždy bezpečné v cloudu**. Vezměte v úvahu následující tři scénáře, ve kterých převzetí služeb při selhání nebyla úspěšně dokončena:
  
  * V počátečních fázích převzetí služeb při selhání, jako je například při provádění předběžné kontroly zotavení po Havárii došlo k chybě. V takovém případě je stále možné použít cílové zařízení. Převzetí služeb při selhání na stejném cílovém zařízení může pokus zopakovat.
  * Během procesu skutečné převzetí služeb při selhání došlo k chybě. V takovém případě je označen cílové zařízení nepoužitelné. Musíte zřídit a nakonfigurovat jiné cílové virtuální pole a použít pro převzetí služeb při selhání.
  * Byl dokončen, po který byl odstraněn zdrojové zařízení převzetí služeb při selhání, ale cílové zařízení má problémy a nemůžou přistupovat žádná data. Data v cloudu je stále bezpečné a je možné snadno načíst vytvořením jiné virtuální pole a poté ho použít jako cílové zařízení pro zotavení po Havárii.

### <a name="deactivate"></a>Deaktivace
Když deaktivujete StorSimple Virtual Array serveru připojení mezi zařízením a odpovídající služby StorSimple Manager. Deaktivace se **trvalé** operace a není možné vrátit zpět. Deaktivované zařízení nejde zaregistrovat ve službě StorSimple Manager znovu. Další informace najdete v části [deaktivace a odstranění StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Mějte následující osvědčené postupy při deaktivaci virtuálního pole:

* Pořiďte snímek v cloudu všechna data před deaktivace virtuálního zařízení. Po deaktivaci virtuálního pole dojde ke ztrátě všech dat místního zařízení. Pořízení snímku cloudu vám umožní obnovit data v pozdější fázi.
* Před deaktivací StorSimple Virtual Array, ujistěte se, že chcete zastavit nebo odstranit klienty a hostitele, které jsou závislé na tomto zařízení.
* Deaktivované zařízení odstraňte, pokud už nepoužíváte, takže nebude nechat nabíhat poplatky.

### <a name="monitoring"></a>Monitorování
Abyste zkontrolovali, že StorSimple Virtual Array průběžný stav v pořádku, budete muset monitorovat pole a zajistit získat informace ze systému, včetně výstrahy. Chcete-li monitorovat celkový stav tohoto virtuálního pole, implementujte následující osvědčené postupy:

* Konfigurace monitorování ke sledování využití disku vaše virtuální pole datového disku, stejně jako disk s operačním systémem. Pokud s technologií Hyper-V, můžete použít ke kombinaci komponent System Center Virtual Machine Manager (SCVMM) a System Center Operations Manager k monitorování hostitelů virtualizace.
* Konfigurace e-mailová oznámení na vaše virtuální pole pro odeslání výstrah v určité úrovně využití.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index vyhledávání a antivirové kontroly aplikací
StorSimple Virtual Array můžete automaticky vrstvení dat z místní vrstvy do cloudu Microsoft Azure. Při použití aplikace například prohledání indexu nebo antivirovou kontrolu kontrolovat data uložená na StorSimple potřebujete zajistíme, že Cloudová data neobsahuje získat přístup a vyžádá zpět na místní úrovni.

Doporučujeme implementovat následující osvědčené postupy při konfiguraci skenování indexu vyhledávání nebo virus na vaše virtuální pole:

* Zakažte všechny operace automaticky konfigurované úplnou kontrolu.
* Vrstvené svazky nakonfigurujte aplikaci index vyhledávání nebo virus kontroly provádět přírůstkové skenování. To by kontrolovat pouze nová data pravděpodobně umístěný na u místní vrstvy. Během operace přírůstkové není přístupu k datům, která je Vrstvená do cloudu.
* Zajistěte správné vyhledávací filtry a nastavení jsou nakonfigurovány tak, aby získat jenom odpovídající typy souborů zkontrolovaných. Například obrázkových souborů (JPEG, ve formátu GIF a TIFF) a technické výkresy by neměl být byl zkontrolován během opětovné sestavení indexu přírůstkové a úplné.

Pokud používáte Windows indexování procesu, postupujte podle následujících pokynů:

* Nepoužívejte Windows indexeru pro vrstvené svazky, protože připomíná velkého objemu dat (TB) z cloudu, pokud je potřeba často znovu sestavit index. Znovu sestavit index by načíst všechny typy souborů pro indexování jejich obsah.
* Použití Windows indexování proces pro místně připojené svazky, protože to by jenom přístup k datům v místních vrstvách a vytvořit index (cloudových dat nebudou přístupné).

### <a name="byte-range-locking"></a>Uzamčení rozsah bajtů
Aplikace můžete uzamknout zadaný rozsah bajtů v souborech. Pokud na aplikace, které jsou zápisu do StorSimple je povolený zámek rozsah bajtů, pak ovládání datových vrstev nebude fungovat na vaše virtuální pole. Pro vrstvení na práci, by měla být všechny oblasti soubory zobrazovány odemknout. Uzamčení rozsah bajtů není podporována u vrstvené svazky na vaše virtuální pole.

Doporučené opatření, abychom omezili uzamčení rozsah bajtů patří:

* Vypněte rozsah bajtů zamykání v logice aplikace.
* Použít místně připojené svazky (místo víceúrovňová) pro data přidružená k této aplikaci. Místně připojené svazky nejsou vrstvy do cloudu.
* Při použití místně připojené svazky s povoleno uzamčení rozsah bajtů, můžou mít svazek online předtím, než se obnovení dokončí. V těchto případech je nutné počkat na dokončení obnovení.

## <a name="multiple-arrays"></a>Několik polí
Více virtuální pole může být potřeba nasadit na účet pro rostoucí pracovní sady dat, která by mohla úniky do cloudu, proto by to mělo dopad na výkon zařízení. V těchto případech doporučujeme zařízení škálování s růstem pracovní sady. To vyžaduje, aby jeden nebo více zařízení přidat v místním datovém centru. Když přidáte zařízení, můžete:

* Rozdělte aktuální sadu data.
* Nasazení nových úloh do zařízení.
* Pokud nasazujete více virtuální pole, doporučujeme, aby z vyrovnávání zatížení perspektivy, distribuovat pole v hypervisoru různých hostitelích.
* V distribuovaných Namespace systému souborů je možné nasadit více virtuální pole (Pokud je nakonfigurovaný jako souborový server nebo iSCSI server). Podrobný postup najdete v části [distribuované souboru systému Namespace řešení s hybridní cloudové úložiště Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Replikace systému distribuovaných souborů aktuálně není doporučeno používat s virtuální pole. 

## <a name="see-also"></a>Další informace najdete v tématech
Zjistěte, jak [spravovat StorSimple Virtual Array](storsimple-virtual-array-manager-service-administration.md) prostřednictvím služby StorSimple Manager.

