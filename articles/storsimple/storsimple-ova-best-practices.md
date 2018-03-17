---
title: "Osvědčené postupy pro pole virtuální zařízení StorSimple | Microsoft Docs"
description: "Popisuje osvědčené postupy pro nasazení a Správa pole virtuální zařízení StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: alkohli
ms.openlocfilehash: 46fd818d8ca15515c91bb6e65e99b0a3bc1f1fa4
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="storsimple-virtual-array-best-practices"></a>Osvědčené postupy pole virtuální zařízení StorSimple
## <a name="overview"></a>Přehled
Microsoft Azure StorSimple virtuální pole je řešení integrované úložiště, který spravuje úlohy úložiště mezi virtuální zařízení s místně spuštěnou v hypervisoru a cloudového úložiště Microsoft Azure. Pole virtuální zařízení StorSimple je alternativu efektivní a nákladově efektivní k poli fyzické řady 8000. Virtuální pole můžete spustit na vaší stávající infrastruktury hypervisoru, podporuje iSCSI a protokoly SMB a pro vzdálené office/pobočkách vhodné. Další informace o řešení StorSimple, přejděte na [přehled Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Tento článek popisuje osvědčené postupy implementována během počáteční instalace, nasazení a správu pole virtuální zařízení StorSimple. Tyto doporučené postupy pro poskytují ověřené pokyny pro nastavení a správu virtuálních pole. Tento článek je zaměřena na správce IT, kteří nasadit a spravovat virtuální pole ve svých datacentrech.

Doporučujeme, abyste pravidelné kontroly osvědčených postupů k zajištění, že zařízení je stále v dodržování předpisů při změně instalační nebo provozní toku. Měli jste dojde k potížím při implementaci těchto osvědčených postupů na vaše virtuální pole [kontaktovat Microsoft Support](storsimple-virtual-array-log-support-ticket.md) o pomoc.

## <a name="configuration-best-practices"></a>Osvědčenými postupy konfigurace
Tyto doporučené postupy zahrnují pokyny, které musí být sledována během počáteční instalace a nasazení virtuálních polí. Tyto doporučené postupy zahrnují související zřízení virtuálního počítače, nastavení zásad skupiny, změna velikosti, nastavení sítě, konfiguraci účtů úložiště a vytváření sdílených složek a svazků pro virtuální pole. 

### <a name="provisioning"></a>Zřizování
Pole virtuální zařízení StorSimple je virtuální počítač (VM) zřízený v hypervisoru (Hyper-V nebo VMware) hostitelského serveru. Při zřizování virtuálního počítače, zkontrolujte, že váš hostitel je schopen věnovaly dostatek prostředků. Další informace, přejděte na [minimální požadavky na zdroje](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) ke zřízení pole.

Implementujte následující osvědčené postupy při zřizování virtuální pole:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ virtuálního počítače** |**2. generace** virtuálního počítače pro použití se systémem Windows Server 2012 nebo novější a *.vhdx* bitové kopie. <br></br> **Generace 1** virtuálního počítače pro použití se systémem Windows Server 2008 nebo novějším a *VHD* bitové kopie. |Verze virtuálního počítače pomocí 8 při použití *vmdk* bitové kopie. |
| **Typ paměti** |Konfigurace jako **statická paměť**. <br></br> Nepoužívejte **dynamické paměti** možnost. | |
| **Datový typ disku** |Zřízení jako **dynamicky se zvětšující**.<br></br> **Pevná velikost** trvá příliš dlouho. <br></br> Nepoužívejte **rozdílových** možnost. |Použití **dynamického zřizování** možnost. |
| **Úprava dat disku** |Rozšíření nebo zmenšení není povoleno. Pokus o Uděláte to tak vede ke ztrátě všech místních dat v zařízení. |Rozšíření nebo zmenšení není povoleno. Pokus o Uděláte to tak vede ke ztrátě všech místních dat v zařízení. |

### <a name="sizing"></a>Nastavení velikosti
Když pro změnu velikosti pole virtuální zařízení StorSimple, zvažte následující faktory:

* Místní rezervaci pro svazky nebo sdílené složky. Přibližně 12 % prostoru je vyhrazená na místní úrovni pro každou zřízené vrstvený svazek nebo sdílenou složku. Také je přibližně 10 % prostoru vyhrazený pro místně vázaný svazek pro systém souborů.
* Režie snímku. Přibližně 15 % místa na místní úrovni je vyhrazená pro snímky.
* Třeba pro obnovení. Pokud to obnovit jako novou operaci, nastavení velikosti by měl účet pro prostor potřebný pro obnovení. Sdílené složky nebo svazku stejnou velikost je provést obnovení.
* Některé vyrovnávací paměti by měla být přidělená pro všechny neočekávané růstu.

Na základě na předchozích faktorech, požadavky na velikost může být reprezentovaný následující rovnice:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Následující příklady znázorňují, jak můžete velikost virtuálního pole podle svých požadavků.

#### <a name="example-1"></a>Příklad 1:
Na virtuální pole chcete mít možnost

* zřídit 2 TB vrstvený svazek nebo sdílenou složku.
* zřídit 1 TB vrstvený svazek nebo sdílenou složku.
* zřídit 300 GB místně vázaný svazek nebo sdílenou složkou.

Pro předchozí svazky nebo sdílené složky dejte nám vypočítejte požadavky na místo na místní úrovni.

Nejprve pro každý vrstvený svazek nebo sdílenou složku, místní rezervace by být roven 12 % velikosti svazku nebo sdílené složky. Pro místně vázaný svazek nebo sdílenou složku místní rezervace je 10 % velikosti místně vázaný svazek nebo sdílenou složku (kromě velikost zřízeného). V tomto příkladu potřebujete

* Místní rezervace 240 GB (2 TB víceúrovňová sdílení nebo svazku)
* Místní rezervace 120 GB (1 TB víceúrovňová sdílení nebo svazku)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (přidání 10 % místní rezervace velikosti 300 GB zřízený)

Celkové požadované místo na místní vrstvy, pokud je: 240 GB + 120 GB + 330 GB = 690 GB.

Druhý potřebujeme alespoň tolik místa na místní úrovni jako největší jeden rezervace. Toto množství navíc se používá v případě, že potřebujete obnovit z cloudový snímek. V tomto příkladu je největší místní rezervace 330 GB (včetně rezervace pro systém souborů), takže byste přidali, že 690 GB: 690 GB + 330 GB = 1020 GB.
Pokud jsme provedli následující další obnovení, jsme vždy uvolněte místo z předchozí operace obnovení.

Třetí potřebujeme vaše celkové volné místo, pokud k uložení místní snímky, 15 %, aby pouze 85 % je k dispozici. V tomto příkladu, který byl kolem 1020 GB = 0.85&ast;zřízené data na disku TB. Ano, bude zřízené datový disk (1020&ast;(1/0.85)) = 1 200 GB = 1,20 TB ~ 1,25 TB (zaokrouhlení na nejbližší QUARTIL)

Řešení v neočekávané růstu a nové obnovení, by měl zřídit místní disk kolem 1,25-1,5 TB.

> [!NOTE]
> Doporučujeme také, zda je místní disk tence zřízený. Toto doporučení je, protože místo obnovení je potřeba, pouze pokud chcete obnovit data, která je starší než pět dní. Obnovení na úrovni položek umožňuje obnovení dat za posledních pět dní bez nutnosti volné místo pro obnovení.


#### <a name="example-2"></a>Příklad 2:
Na virtuální pole chcete mít možnost

* zřídit 2 TB vrstvené svazku
* 300 GB místně vázaný svazek zřídit

Podle % 12 rezervace volné místo pro vrstvené svazky nebo sdílené složky a 10 % místně vázaných svazků nebo sdílených složek, potřebujeme

* Místní rezervace 240 GB (2 TB víceúrovňová sdílení nebo svazku)
* 330 GB pro místně vázaný svazek nebo sdílenou složku (přidání 10 % místní rezervace do místa zřizovat 300 GB)

Celkové požadované místo na místní vrstvy je: 240 GB + 330 GB = 570 GB

Minimální volné místo potřebné pro obnovení je 330 GB.

15 % celkové disku se používá pro ukládání snímků tak, aby pouze 0.85 je k dispozici. Ano, je velikost disku (900&ast;(1/0.85)) = 1.06 TB ~ 1,25 TB (zaokrouhlení na nejbližší QUARTIL)

Řešení v jakékoli neočekávané růst, můžete zřídit na místní disk 1,25-1,5 TB.

### <a name="group-policy"></a>Zásady skupiny
Zásady skupiny jsou infrastruktura, která umožňuje implementovat konkrétní konfigurace pro uživatele a počítače. Nastavení zásad skupiny jsou obsažené v objektech zásad skupiny (GPO), které jsou propojeny s následující kontejnery služby Active Directory Domain Services (AD DS): weby, domény nebo organizační jednotky (OU). 

Pokud vaše virtuální pole je připojený k doméně, může na něho použít objekty zásad skupiny. Tyto objekty zásad skupiny můžete nainstalovat aplikace, jako je antivirový software, který může nepříznivě ovlivnit operaci pole virtuální zařízení StorSimple.

Proto doporučujeme, aby vám:

* Zajistěte, aby byl virtuální pole ve vlastní organizační jednotku (OU) pro službu Active Directory.
* Ujistěte se, že žádné objekty zásad skupiny (GPO) se použijí pro vaše virtuální pole. Můžete zablokovat dědičnost zajistit, že virtuální pole (podřízený uzel) nedědí automaticky žádnými objekty zásad skupiny z nadřazeného objektu. Další informace, přejděte na [zablokovat dědičnost](https://technet.microsoft.com/library/cc731076.aspx).

### <a name="networking"></a>Sítě
Konfigurace sítě pro vaše virtuální pole se provádí prostřednictvím místního webového uživatelského rozhraní. Rozhraní virtuální sítě je povolená díky hypervisoru, ve kterém je zřízený virtuální pole. Použití [nastavení sítě](storsimple-virtual-array-deploy3-fs-setup.md) můžete nakonfigurovat IP adresu rozhraní virtuální sítě, podsítě a bránu.  Můžete také nakonfigurovat primární a sekundární server DNS, nastavení času a volitelná nastavení proxy serveru pro vaše zařízení. Většinu konfiguraci sítě je jednorázové instalační program. Zkontrolujte [StorSimple sítě požadavky](storsimple-ova-system-requirements.md#networking-requirements) před nasazením virtuální pole.

Pokud nasazujete virtuální pole, doporučujeme dodržovat tyto osvědčené postupy:

* Zajistěte, aby síť, ve kterém je virtuální pole nasazená vždy kapacitu vyhradit 5 šířky pásma Internetu MB/s (nebo více).
  
  * Třeba šířky pásma Internetu se liší v závislosti na vaší charakteristiky zatížení a rychlosti změny dat.
  * Změny dat, která může být zpracována je přímo úměrná pásma vašeho internetového připojení. Například při zálohování 5 MB/s šířky pásma zvládne přibližně 18 GB změny dat v 8 hodin. Čtyřikrát větší šířka pásma (20 MB/s) může zpracovávat čtyřikrát další změny dat (72 GB).
* Zkontrolujte, zda je vždy k dispozici připojení k Internetu. Připojení k Internetu výskyt občasný, nebo nespolehlivé na zařízení může dojít ke ztrátě přístupu k datům v cloudu a může mít za následek nepodporované konfigurace.
* Pokud plánujete nasazení zařízení jako iSCSI server:
  
  * Doporučujeme zakázat **získat IP adresu automaticky** možnost (DHCP).
  * Konfigurace statické IP adresy. Je nutné nakonfigurovat primární a sekundární server DNS.
  * Pokud definování více síťových rozhraní na vaše virtuální pole, pouze první síťové rozhraní (ve výchozím nastavení, toto rozhraní je **Ethernet**) dosáhnout cloudu. Chcete-li řídit typ provozu, můžete vytvořit více rozhraní virtuální sítě na vaše virtuální pole (nakonfigurovaný jako server se službou iSCSI) a připojení těchto rozhraní k různým podsítím.
* Omezení šířky pásma cloudu pouze (používá pole virtuální), konfigurace omezení na směrovači nebo brány firewall. Pokud definujete omezení ve vašem hypervisoru, bude ho omezení všechny protokoly, včetně iSCSI a SMB místo právě cloudu šířky pásma.
* Ujistěte se, že synchronizaci času pro hypervisory je povoleno. Pokud vaše virtuální pole pomocí technologie Hyper-V, vyberte ve Správci technologie Hyper-V, přejděte na **nastavení &gt; integrační služby**a ujistěte se, že **synchronizace času** je zaškrtnuté.

### <a name="storage-accounts"></a>Účty úložiště
Pole virtuální zařízení StorSimple může být přidružena k účtu jedno úložiště. Tento účet úložiště může být účet úložiště automaticky generovaného účtu ve stejném předplatném jako služba, nebo do jiného předplatného souvisejících s účtem úložiště. Další informace najdete v tématu Jak [Správa účtů úložiště pro vaše virtuální pole](storsimple-virtual-array-manage-storage-accounts.md).

Pomocí následujících doporučení pro účty úložiště přidružené virtuální pole.

* Při propojování více virtuální pole s účtem jedno úložiště, zohlednit maximální kapacity (64 TB) pro virtuální pole a maximální velikost (500 TB) pro účet úložiště. To omezuje počet plné velikosti virtuální pole, které lze přidružit k tomuto účtu úložiště do asi 7.
* Při vytváření nového účtu úložiště
  
  * Doporučujeme vytvořit ji v oblasti nejblíže k vzdáleného office nebo firemní pobočku, kde je nasazená pole virtuální zařízení StorSimple pro minimalizaci latence.
  * Berte v úvahu, že nemůžete přesunout účet úložiště v různých oblastech. Službu nelze také přesouvat mezi odběry.
  * Použijte účet úložiště, který implementuje redundance mezi v datacentrech. GEO-Redundant Storage (GRS), zóny redundantní úložiště (ZRS) a místně redundantní úložiště (LRS) jsou všechny podporované pro použití s virtuální pole. Další informace o různých typech účtů úložiště, přejděte na [replikace Azure storage](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Sdílené složky a svazky
Pro pole virtuální zařízení StorSimple můžete zřídit sdílené složky, když je nakonfigurovaný jako souborový server a svazky, když je nakonfigurovaný jako server se službou iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazky se vztahují na velikost a typ konfigurovaný.

#### <a name="volumeshare-size"></a>Velikost svazku nebo sdílené složky
Na virtuální pole můžete zřídit sdílené složky, když je nakonfigurovaný jako souborový server a svazky, když je nakonfigurovaný jako server se službou iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazky se týkají velikost a typ konfigurovaný. 

Při zřizování sdílené složky nebo svazky na virtuální zařízení mějte na paměti následující osvědčené postupy.

* Velikosti souborů relativně k velikost zřízeného vrstvené sdílené složky může ovlivnit vrstvení výkon. Práce s velkými soubory by mohla způsobit pomalé vrstvy. Při práci s velkých souborů, doporučujeme vám, že na největší soubor je menší než velikost sdílené složky % 3.
* Na virtuální pole lze vytvořit maximálně 16 svazky nebo sdílené složky. Limity velikosti místně vázaný a vrstvené svazky nebo sdílené složky, vždy najdete v části [omezuje pole virtuální zařízení StorSimple](storsimple-ova-limits.md).
* Při vytváření svazku, faktorem při spotřebě očekávaným objemem dat a také budoucímu růstu. Svazek nelze rozšířit později.
* Po vytvoření svazku nelze zmenšit velikost svazku v zařízení StorSimple.
* Při zápisu do vrstvený svazek v zařízení StorSimple, když data svazek dosáhne určitým prahem (relativní vůči volné místo vyhrazené pro daný svazek), je omezen vstupně-výstupní operace. Pokračovat k zápisu do tohoto svazku zpomaluje vstupně-výstupní výrazně. I když můžete zapisovat vrstvený svazek nad rámec jeho zřízená kapacita (jsme nedojde k zastavení aktivně uživateli zápis nad rámec zřízená kapacita), zobrazí se oznámení o tom, které mají oversubscribed výstrahy. Jakmile se zobrazí výstraha, je nutné provést nápravné opatření, například odstranit data na svazku (svazek rozšíření není aktuálně podporována.).
* Pro případy použití pro zotavení po havárii jako je počet povolených sdílených složek nebo svazků 16 a maximální počet sdílené složky nebo svazky, které lze zpracovat paralelní je také 16, počtu sdílených složek nebo svazků nemá vliv na plánovaný bod obnovení a RTO.

#### <a name="volumeshare-type"></a>Typ svazku nebo sdílené složky
StorSimple podporuje dva typy sdílení nebo svazku na základě využití: místně ukotvena a vrstvené. Místně vázaných svazků nebo sdílených složek je tlustě zřízený, zatímco jsou dynamicky zajišťované vrstvené svazky nebo sdílené složky. Nelze převést místně vázaný svazek nebo sdílenou složku na vrstvené nebo *naopak* po vytvoření.

Doporučujeme implementovat následující osvědčené postupy, při konfiguraci StorSimple svazky nebo sdílené složky:

* Určete typ svazku založené na jiné úlohy, které máte v úmyslu nasadit před vytvořením svazku. Použijte pro úlohy, které vyžadují místní záruky dat (i během výpadku cloud) a které vyžadují, nízkou cloudu latence místně vázaných svazků. Jakmile vytvoříte svazek na vaše virtuální pole, nelze změnit typ svazku z místně vázaný na vrstvené nebo *naopak*. Jako příklad vytváření místně vázaných svazků při nasazování SQL úlohy nebo úlohy hostování virtuálních počítačů (VM); použijte pro úlohy sdílené složky souboru vrstvené svazky.
* Zaškrtněte možnost pro archivní data s méně často používaných při plánování práce s velikostí velkých souborů. Větší velikost bloku dat odstranění duplicit 512 kB se používá, pokud je tato možnost povolena urychlit přenos dat do cloudu.

#### <a name="volume-format"></a>Formát svazku
Po vytvoření svazky zařízení StorSimple na vašem serveru iSCSI, musíte inicializovat, připojení a formátování svazků. Tato operace se provádí na hostiteli připojen k zařízení StorSimple. Doporučujeme následující osvědčené postupy při připojení a formátování svazků na hostiteli StorSimple.

* Proveďte rychlé formátování na všechny svazky zařízení StorSimple.
* Při formátování svazek StorSimple, použijte velikost alokační jednotky (Austrálie) 64 kB (výchozí hodnota je 4 KB). 64 KB Austrálie je založena na testování provádí interně pro běžné úlohy StorSimple a další úlohy.
* Pokud používáte pole virtuální zařízení StorSimple, který je nakonfigurovaný jako server se službou iSCSI, nepoužívejte rozložené svazky nebo dynamické disky jako tyto svazky nebo disky nejsou podporovány StorSimple.

#### <a name="share-access"></a>Přístup ke sdílené složce
Při vytváření sdílené složky na souborovém serveru virtuální pole, postupujte podle těchto pokynů:

* Když vytváříte sdílenou složku, přiřadíte skupinu uživatelů jako správce a sdílené složky namísto jednoho uživatele.
* Po vytvoření sdílené složky tak, že upravíte sdílených složek pomocí Průzkumníka Windows, může spravovat oprávnění systému souborů NTFS.

#### <a name="volume-access"></a>Přístup ke svazku
Při konfiguraci iSCSI svazky na pole virtuální zařízení StorSimple, je důležité pro řízení přístupu, kdykoli je to nutné. Pokud chcete zjistit, které hostitelské servery můžete získat přístup ke svazkům, vytvořte a přidružte svazky zařízení StorSimple záznamy řízení přístupu (ACRs).

Při konfiguraci ACRs pro svazky zařízení StorSimple, použijte následující osvědčené postupy:

* Alespoň jeden ACR vždy přidružte svazku.

* Při přiřazování ACR více než jeden svazek, ujistěte se, že svazek není vystavení způsobem, kde můžete současně přistupovat ve více než jeden neclusterovaného hostitele. Pokud jste přiřadili více ACRs na svazek, upozornění se zobrazí pro vás k posouzení konfiguraci.

### <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat
Pole virtuální zařízení StorSimple je funkce zabezpečení a šifrování dat, které zajistit důvěrnost a integritu dat. Při používání těchto funkcí, doporučujeme dodržovat tyto osvědčené postupy: 

* Zadejte šifrovací klíč cloudového úložiště pro generování šifrování AES 256 před odesláním dat z vaší virtuální pole do cloudu. Tento klíč se nevyžaduje, pokud vaše data se šifrují na začátku. Klíč může generovat a uchovávat bezpečné pomocí systémem správy klíčů, jako třeba [Azure trezoru klíčů](../key-vault/key-vault-whatis.md).
* Při konfiguraci účtu úložiště prostřednictvím služby StorSimple Manager, zkontrolujte, zda povolit režim SSL vytvořit zabezpečený kanál pro síťovou komunikaci mezi zařízení StorSimple a cloudem.
* Vygenerování klíčů pro účty úložiště (podle přístupu ke službě Azure Storage) pravidelně na účet pro přístup k veškeré změny podle změněné seznamu správců.
* Data na vaše virtuální pole jsou komprimované a odstranění duplicit před odesláním do služby Azure. Nedoporučujeme používat službu role odstranění duplicitních dat na hostiteli s Windows Server.

## <a name="operational-best-practices"></a>Aplikovatelné nejlepší postupy
Aplikovatelné nejlepší postupy jsou pokyny, které musí být sledována během operace virtuální pole nebo každodenní správu. Tyto postupy zahrnují určité úlohy správy například umožňoval vytvářet zálohy, obnovení ze zálohovacího skladu, provádění převzetí služeb při selhání, deaktivace a odstranění poli monitorování systému využití a stavu a spuštění virů kontrol na vaše virtuální pole.

### <a name="backups"></a>Zálohování
Data na vaše virtuální pole je zálohována do cloudu dvěma způsoby, výchozí automatizované denní zálohování celého zařízení spouštění ve 22:30 nebo prostřednictvím ručního zálohování na vyžádání. Ve výchozím nastavení zařízení automaticky vytvoří denní cloudových snímků všechna data, které se nacházejí na něm. Další informace, přejděte na [zálohování pole virtuální zařízení StorSimple](storsimple-virtual-array-backup.md).

Nelze změnit četnost a uchovávání, přidružené zálohy výchozí ale můžete nakonfigurovat čas, kdy denní zálohy se spouští každý den. Při konfiguraci čas zahájení pro automatické zálohování, doporučujeme:

* Plán zálohování pro hodiny mimo špičku. Pokud čas spuštění zálohování se neshoduje se množství hostitele vstupně-výstupní operace.
* Spustit ruční zálohování na vyžádání při plánování provedení převzetí služeb při selhání zařízení nebo před pro správu a údržbu k ochraně dat ve vaší virtuální pole.

### <a name="restore"></a>Obnovení
Můžete obnovit ze zálohy nastavit dvěma způsoby: obnovení do jiného svazku nebo sdílené složky nebo provádět obnovení na úrovni položek (k dispozici pouze u virtuálních pole nakonfigurovaný jako souborový server). Obnovení na úrovni položek lze provádět podrobné obnovení souborů a složek z cloudu zálohu všechny sdílené složky v zařízení StorSimple. Další informace, přejděte na [obnovit ze zálohy](storsimple-virtual-array-clone.md).

Při provádění obnovení, mějte tyto pokyny:

* Pole virtuální zařízení StorSimple nepodporuje obnovení na místě. To může ale být snadno dosáhnout dvoustupňový proces: Uvolněte na virtuální pole a pak obnovení do jiného svazku nebo sdílené složky.
* Při obnovení na místním svazku, mějte na paměti obnovení bude operace probíhající dlouhou dobu. V případě, že svazek může rychle režimu online, data i nadále možné HYDRATOVANÝ na pozadí.
* Typ svazku zůstane stejný během procesu obnovení. Vrstvený svazek je obnovena na jiné vrstvený svazek a místně vázaný svazek na jiný místně připnutý svazku.
* Při pokusu o obnovení ze zálohy na svazek nebo sdílenou složku, pokud úloha obnovení selže, nastavte cílový svazek nebo sdílenou složku stále možné vytvářet na portálu. Je důležité odstranit tento nepoužívané cílový svazek nebo sdílenou složku na portálu, chcete-li minimalizovat budoucí potíží vzniklých tohoto elementu.

### <a name="failover-and-disaster-recovery"></a>Převzetí služeb při selhání a zotavení po havárii
Selhání zařízení umožňuje migrovat data z *zdroj* zařízení v datovém centru do jiného *cíl* zařízení umístěné ve stejné nebo jiné zeměpisné umístění. Převzetí služeb při selhání zařízení je ze zařízení. Během převzetí služeb při selhání Cloudová data zdrojového zařízení změny vlastnictví u cílové zařízení.

Pro pole virtuální zařízení StorSimple můžete můžete pouze převzetí služeb při selhání na jiný virtuální pole spravuje stejnou službu StorSimple Manager. Převzetí služeb při selhání do zařízení se systémem řady 8000 nebo pole spravovány jinou službu StorSimple Manager (než ten, který pro zdrojového zařízení) není povoleno. Další informace o aspektech převzetí služeb při selhání, přejděte na [požadavky pro převzetí služeb při selhání zařízení](storsimple-virtual-array-failover-dr.md).

Při provádění selhání přes pro vaše virtuální pole, mějte následující:

* Plánované převzetí služeb je součástí osvědčeného postupu provést všechny svazky nebo sdílené složky offline před zahájením převzetí služeb při selhání. Postupujte podle pokynů specifické pro operační systém a trvat svazky nebo sdílené složky do offline režimu na hostiteli první a pak proveďte ty offline na virtuální zařízení.
* Souborový server zotavení po havárii (DR) doporučujeme, aby se automaticky vyřeší oprávnění ke sdílení připojení cílového zařízení do stejné domény jako zdroj. V této verzi je podporována pouze převzetí služeb při selhání na cílovém zařízení ve stejné doméně.
* Po úspěšném dokončení DR zdrojového zařízení se automaticky odstraní. Když zařízení již není k dispozici, je virtuální počítač, který jste zřídili v hostitelském systému nadále spotřebovávají prostředky. Doporučujeme odstranit tento virtuální počítač z hostitele systému a zabránit nabíhání poplatků.
* Všimněte si, že i když převzetí služeb při selhání je úspěšné, **data je bezpečné vždy v cloudu**. Vezměte v úvahu následující tři scénáře, ve kterých převzetí služeb při selhání nebyla úspěšně dokončena:
  
  * Došlo k chybě v úvodní fázích převzetí služeb při selhání, například když se provádí předběžné kontroly zotavení po Havárii. V takovém případě je stále možné použít cílové zařízení. Může pokus zopakovat převzetí služeb při selhání na stejné cílové zařízení.
  * Došlo k chybě během procesu skutečné převzetí služeb při selhání. V takovém případě je označena cílové zařízení nepoužitelný. Musíte zřídit a nakonfigurovat jiné cílové virtuální pole a použijte k převzetí služeb při selhání.
  * Dokončení, po který byl odstraněn zdrojového zařízení se převzetí služeb při selhání, ale cílové zařízení má problémy a nemůžou přistupovat žádná data. Data jsou stále bezpečné v cloudu a dá snadno načíst vytváření jiný virtuální pole a následným použitím jako cílové zařízení pro zotavení po Havárii.

### <a name="deactivate"></a>Deaktivace
Pokud deaktivujete o pole virtuální zařízení StorSimple, server se připojení mezi zařízením a odpovídající služby StorSimple Manager. Deaktivace je **trvalé** operace a nelze ji vrátit zpět. Deaktivované zařízení nemůže být zaregistrován u služby StorSimple Manager znovu. Další informace, přejděte na [deaktivovat a odstranit pole virtuální zařízení StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Následující osvědčené postupy při mějte deaktivace virtuální pole:

* Pořízení snímku cloudu všechna data před deaktivace virtuálního zařízení. Pokud deaktivujete virtuální pole, dojde ke ztrátě všech dat místní zařízení. Pořízení snímku cloudu vám umožní obnovit data v pozdější fázi.
* Před deaktivací o pole virtuální zařízení StorSimple, zajistěte, aby pro zastavení nebo odstranit klienty a hostitele, které jsou závislé na tomto zařízení.
* Odstraňte deaktivované zařízení, pokud už používáte, takže ho nebude nabíhat poplatky.

### <a name="monitoring"></a>Monitorování
Zajistěte, aby vaše pole virtuální zařízení StorSimple v průběžné stavu v pořádku, musíte ke sledování pole a ujistěte se, zda se zobrazila informace ze systému, včetně výstrahy. Ke sledování celkového stavu virtuální pole, implementujte následující osvědčené postupy:

* Konfigurace monitorování ke sledování využití disku virtuální pole datový disk jako disk operačního systému. Pokud s technologií Hyper-V, můžete použít kombinaci System Center Virtual Machine Manager (SCVMM) a System Center Operations Manager (SCOM) k monitorování hostitelů virtualizace.
* Konfigurace e-mailová oznámení na vaše virtuální pole pro odeslání výstrah na určité úrovni využití.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Index vyhledávání a antivirové kontroly aplikací
Pole virtuální zařízení StorSimple můžete automaticky vrstvy dat z místní vrstvy do cloudu Microsoft Azure. Pokud aplikace například prohledání indexu nebo antivirovou kontrolu slouží ke kontrole dat uložených na zařízení StorSimple, budete muset Ujistěte se, že data v cloudu nepodporuje získat přístup a vyžádat zpět do místní vrstvy.

Doporučujeme implementovat následující osvědčené postupy, při konfiguraci skenování indexu vyhledávání nebo virů na vaše virtuální pole:

* Zakažte všechny operace automaticky konfigurované úplnou kontrolu.
* Pro vrstvené svazky nakonfigurujte aplikaci indexu vyhledávání nebo antivirové kontroly provedení přírůstkových kontroly. To by kontrolu pouze nové dat pravděpodobně umístěný na místní úrovni. Během operace přírůstkové není přístupu k datům, který vrstveny do cloudu.
* Zkontrolujte nastavení a hledání správné filtry jsou nakonfigurovány tak, aby získat zkontrolovat určený typy souborů. Například bitovou kopii souborů (JPEG, GIF a TIFF) a technici výkresy nesmí zkontrolován během index úplné nebo přírůstkové sestavení.

Pokud používáte Windows indexování procesu, postupujte podle následujících pokynů:

* Nepoužívejte Windows indexeru pro vrstvené svazky, protože vyhledává velké objemy dat (TBs) z cloudu, pokud potřebujete často znovu sestavit index. Znovu sestavit index by načíst všechny typy souborů do indexu obsah.
* Použití Windows indexování proces místně vázaných svazků, jako to by pouze přístup k datům v místních vrstvách a sestavení indexu (Cloudová data nebudou přístupná).

### <a name="byte-range-locking"></a>Zamykání rozsahu bajtů
Aplikace můžete uzamknout zadaný rozsah bajtů v souborech. Pokud v aplikacích, které jsou zápis do vašeho zařízení StorSimple je povolený zámek rozsah bajtů, pak vrstvení nefunguje na vaše virtuální pole. Pro vrstvení fungovat, musí být všechny oblasti souborů získat přístup k odemknout. Zamykání rozsah bajtů není podporována u vrstvené svazky na vaše virtuální pole.

Doporučená opatření ke zmírnění zamykání rozsah bajtů patří:

* Vypněte rozsah bajtů zamykání logiky aplikace.
* Použití místně připnutý svazky (místo víceúrovňová) pro data přidružená k této aplikaci. Místně vázaných svazků není vrstvy do cloudu.
* Při použití místně vázaný svazky s povoleno uzamčení rozsah bajtů, můžou mít svazek online před dokončením obnovení. V těchto případech je nutné počkat na dokončení obnovení.

## <a name="multiple-arrays"></a>Několik polí
Několik polí virtuální chtít nasadit na účet pro rostoucí pracovní sadu dat, která by mohla distribuována do cloudu, tak ovlivnit výkon zařízení. V těchto případech je nejvhodnější pro škálování zařízení s růstem pracovní sady. To vyžaduje jeden nebo více zařízení k přidání do místního datového centra. Při přidávání zařízení, může:

* Rozdělení aktuální sadu dat.
* Nasazení nových úloh do nového zařízení.
* Pokud nasazení několik virtuálních polí, doporučujeme, aby ze služby Vyrovnávání zatížení perspektivy, distribuovat do hostitelů hypervisoru jiné pole.
* Několik virtuálních polí (Pokud je nakonfigurovaný jako souborový server nebo server se službou iSCSI) se dá nasadit v distribuované Namespace systému souborů. Podrobné kroky, přejděte na [distribuované soubor řešení systému Namespace s hybridní cloudové úložiště Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507). Replikaci distribuovaného systému souborů se aktuálně nedoporučuje pro použití s poli virtuální. 

## <a name="see-also"></a>Další informace najdete v tématech
Zjistěte, jak [spravovat vaše pole virtuální zařízení StorSimple](storsimple-virtual-array-manager-service-administration.md) přes službu StorSimple Manager.

