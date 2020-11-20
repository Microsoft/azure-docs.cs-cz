---
title: Osvědčené postupy pro virtuální pole StorSimple | Microsoft Docs
description: Popisuje osvědčené postupy pro nasazení a správu virtuálního pole StorSimple.
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
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964979"
---
# <a name="storsimple-virtual-array-best-practices"></a>Osvědčené postupy pro StorSimple Virtual Array

## <a name="overview"></a>Přehled

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Microsoft Azure StorSimple Virtual Array je integrované řešení úložiště, které spravuje úlohy úložiště mezi místním virtuálním zařízením spuštěným v hypervisoru a Microsoft Azure cloudovém úložišti. Virtuální pole StorSimple je efektivní a cenově výhodné alternativou fyzického pole řady 8000. Virtuální pole je možné spustit na stávající infrastruktuře hypervisoru, podporuje protokoly iSCSI a SMB a je vhodná pro vzdálené scénáře vzdáleného Office/pobočky. Další informace o řešeních StorSimple najdete v článku [přehled Microsoft Azure StorSimple](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx).

Tento článek popisuje osvědčené postupy implementované při počáteční instalaci, nasazení a správě virtuálního pole StorSimple. Tyto osvědčené postupy poskytují ověřené pokyny pro nastavení a správu vašeho virtuálního pole. Tento článek je zaměřený na správce IT, kteří nasazují a spravují virtuální pole ve svých datových centrech.

Doporučujeme pravidelně prověřit osvědčené postupy, které vám pomůžou zajistit, aby vaše zařízení bylo pořád v souladu s tím, kdy se změny provedou v průběhu instalace nebo provozu. Pokud při implementaci těchto osvědčených postupů na virtuálním poli narazíte na nějaké problémy, obraťte se na [Podpora Microsoftu](storsimple-virtual-array-log-support-ticket.md) o pomoc.

## <a name="configuration-best-practices"></a>Osvědčené postupy konfigurace
Tyto osvědčené postupy pokrývají pokyny, které je třeba dodržovat při počátečním nastavení a nasazení virtuálních polí. Tyto osvědčené postupy zahrnují ty, které se týkají zřizování virtuálního počítače, nastavení zásad skupiny, určení velikosti, nastavení sítě, konfiguraci účtů úložiště a vytváření sdílených složek a svazků pro virtuální pole. 

### <a name="provisioning"></a>Zřizování
Virtual Array StorSimple je virtuální počítač zřízený na hypervisoru (Hyper-V nebo VMware) hostitelského serveru. Při zřizování virtuálního počítače se ujistěte, že je hostitel schopný vyhradit dostatečné prostředky. Další informace získáte tak, že přejdete na [minimum požadavky na prostředky](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) a zřídíte pole.

Při zřizování virtuálního pole implementovat tyto osvědčené postupy:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ virtuálního počítače** |**Generace 2** Virtuální počítač pro použití s Windows Serverem 2012 nebo novějším a imagí *. vhdx* . <br></br> **Generace 1** Virtuální počítač pro použití s Windows Serverem 2008 nebo novějším a imagí *. VHD* |Při použití image *. vmdk* použijte virtuální počítač verze 8. |
| **Typ paměti** |Nakonfigurujte jako **statickou paměť**. <br></br> Nepoužívejte možnost **dynamické paměti** . | |
| **Typ datového disku** |Zřizuje se jako **dynamicky se zvětšující**.<br></br> **Pevná velikost** trvá dlouhou dobu. <br></br> Nepoužívejte možnost **rozdílu** . |Použijte možnost **dynamického zajišťování** . |
| **Úprava datového disku** |Rozšiřování nebo zmenšování není povoleno. Pokusíte se to udělat tak, že dojde ke ztrátě všech místních dat v zařízení. |Rozšiřování nebo zmenšování není povoleno. Pokusíte se to udělat tak, že dojde ke ztrátě všech místních dat v zařízení. |

### <a name="sizing"></a>Velikosti
Při změně velikosti StorSimple virtuálního pole Vezměte v úvahu následující faktory:

* Místní rezervace pro svazky nebo sdílené složky. Přibližně 12% místa je vyhrazeno na místní úrovni pro každý zřízený vrstvený svazek nebo sdílenou složku. Přibližně 10% prostoru je vyhrazeno také pro místně připnuté svazek pro systém souborů.
* Režie snímku. Pro snímky se rezervuje zhruba 15% místa na místní úrovni.
* Nutné pro obnovení. Pokud se provádí obnovení jako nová operace, mělo by se změna velikosti vystavovat pro prostor potřebný pro obnovení. Obnovení je provedeno do sdílené složky nebo svazku stejné velikosti.
* Pro jakýkoliv neočekávaný nárůst by se měla přidělit nějaká vyrovnávací paměť.

V závislosti na předchozích faktorech se požadavky na velikost můžou znázornit pomocí následující rovnice:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Následující příklady znázorňují, jak můžete velikost virtuálního pole nastavit podle vašich požadavků.

#### <a name="example-1"></a>Příklad 1:
Ve vašem virtuálním poli si přejete mít

* zřídit 2 TB vrstveného svazku nebo sdílené složky.
* zřídit 1 TB vrstveného svazku nebo sdílené složky.
* zřídit 300 GB místně připnutého svazku nebo sdílené složky.

Pro předchozí svazky nebo sdílené složky můžeme vypočítat požadavky na místo na místní úrovni.

Pro každý vrstvený svazek nebo sdílenou složku nejdřív by se místní rezervace rovnala 12% velikosti svazku nebo sdílené složky. U místně připnutého svazku nebo sdílené složky je místní rezervace 10% místně připnutého svazku nebo velikosti sdílené složky (kromě zřízené velikosti). V tomto příkladu potřebujete

* 240-GB místní rezervace (pro 2 TB vrstvený svazek nebo sdílenou složku)
* 120-GB místní rezervace (pro 1 TB vrstvený svazek nebo sdílenou složku)
* 330-GB pro místně připnuté svazek nebo sdílenou složku (přidání 10% místní rezervace do zřízené velikosti 300 GB)

Celkové místo vyžadované na místní úrovni je: 240 GB + 120 GB + 330 GB = 690 GB.

Za druhé potřebujeme aspoň tolik místa na místní úrovni jako největší jedinou rezervaci. Tato částka navíc se používá v případě, že potřebujete obnovení z cloudového snímku. V tomto příkladu je největší místní rezervace 330 GB (včetně rezervace pro systém souborů), takže byste je měli přidat do 690 GB: 690 GB + 330 GB = 1020 GB.
Pokud jsme provedli následné dodatečné obnovení, můžeme vždy uvolnit místo z předchozí operace obnovení.

Třetí, potřebujeme ještě 15% celkového místního prostoru pro ukládání místních snímků, takže je k dispozici jenom 85% z tohoto místa. V tomto příkladu by to představovalo přibližně 1020 GB = 0,85 &ast; zřízený datový disk TB. Proto by byl zřízený datový disk (1020 &ast; (1/0.85)) = 1200 GB = 1,20 TB ~ 1,25 TB (zaokrouhlení na nejbližší kvartil).

V případě neočekávaného nárůstu a nových obnovení byste měli zřídit místní disk o velikosti přibližně 1,25 až 1,5 TB.

> [!NOTE]
> Doporučujeme také, aby byl místní disk dynamicky zřízen. Toto doporučení je způsobeno tím, že prostor obnovení je zapotřebí pouze v případě, že chcete obnovit data, která jsou starší než pět dní. Obnovení na úrovni položek umožňuje obnovit data za posledních pět dní, aniž by vyžadovala dodatečné místo pro obnovení.


#### <a name="example-2"></a>Příklad 2:
Ve vašem virtuálním poli si přejete mít

* zřízení vrstveného svazku se dvěma TB
* zřízení svazku připojeného místně 300 GB

Na základě 12% rezervace místního prostoru pro vrstvené svazky/sdílené složky a 10% pro místně připojené svazky nebo sdílené složky potřebujeme

* 240-GB místní rezervace (pro 2 TB vrstvený svazek nebo sdílenou složku)
* 330-GB pro místně připnuté svazek nebo sdílení (přidání 10% místní rezervace do zřízeného prostoru 300 GB)

Celková velikost požadovaného prostoru na místní úrovni: 240 GB + 330 GB = 570 GB

Minimální místní prostor potřebný k obnovení je 330 GB.

15% celkového disku se používá k ukládání snímků, takže je k dispozici jenom 0,85. Velikost disku je tedy (900 &ast; (1/0.85)) = 1,06 TB ~ 1,25 TB (zaokrouhlení na nejbližší kvartil)

Faktoring při jakémkoli neočekávaném nárůstu můžete zřídit místní disk 1,25-1,5 TB.

### <a name="group-policy"></a>Zásady skupiny
Zásady skupiny je infrastruktura, která umožňuje implementovat konkrétní konfigurace pro uživatele a počítače. Nastavení Zásady skupiny jsou obsažena v objektech Zásady skupiny (objekty zásad skupiny), které jsou propojeny s následujícími kontejnery Active Directory Domain Services (služba AD DS): weby, domény nebo organizační jednotky (OU). 

Pokud je vaše virtuální pole připojené k doméně, můžete na něj použít objekty zásad skupiny. Tyto objekty zásad skupiny můžou instalovat aplikace, jako je antivirový software, který může mít nepříznivý vliv na provoz virtuálního pole StorSimple.

Proto doporučujeme:

* Ujistěte se, že vaše virtuální pole je ve své vlastní organizační jednotce pro službu Active Directory.
* Ujistěte se, že ve virtuálním poli nejsou použité žádné objekty zásad skupiny (GPO). Dědičnost můžete zablokovat, aby se zajistilo, že virtuální pole (podřízený uzel) automaticky nedědí žádné objekty zásad skupiny z nadřazené položky. Další informace najdete na webu [blokování dědičnosti](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

### <a name="networking"></a>Sítě
Konfigurace sítě pro vaše virtuální pole se provádí prostřednictvím místního webového uživatelského rozhraní. Virtuální síťové rozhraní je povolené prostřednictvím hypervisoru, ve kterém je virtuální pole zřízené. Na stránce [nastavení sítě](storsimple-virtual-array-deploy3-fs-setup.md) můžete nakonfigurovat IP adresu, podsíť a bránu virtuálního síťového rozhraní.  Pro vaše zařízení můžete také nakonfigurovat primární a sekundární server DNS, nastavení času a volitelná nastavení proxy serveru. Většina konfigurace sítě je jednorázová instalace. Před nasazením virtuálního pole si Projděte [požadavky na síť StorSimple](storsimple-ova-system-requirements.md#networking-requirements) .

Při nasazování virtuálního pole doporučujeme dodržovat tyto osvědčené postupy:

* Ujistěte se, že síť, ve které je nasazené virtuální pole, má vždy kapacitu k vyhradení 5 MB/s internetovou šířkou pásma (nebo více).
  
  * Šířka pásma internetu se liší v závislosti na charakteristikách úloh a rychlosti změny dat.
  * Změna dat, která může být zpracována, je přímo úměrná šířce pásma internetu. Jako příklad při pořizování zálohy se může při změně velikosti pásma 5 MB/s změnit data o velikosti přibližně 18 GB během 8 hodin. AŽ čtyřikrát větší šířka pásma (20 MB/s), můžete zpracovávat čtyřikrát více změn dat (72 GB).
* Ujistěte se, že připojení k Internetu je vždy k dispozici. Občasné nebo nespolehlivé internetové připojení k zařízením může vést ke ztrátě přístupu k datům v cloudu a může mít za následek nepodporovanou konfiguraci.
* Pokud plánujete nasazení zařízení jako serveru iSCSI:
  
  * Doporučujeme zakázat možnost **získat IP adresu automaticky** (DHCP).
  * Konfigurace statických IP adres. Je nutné nakonfigurovat primární a sekundární server DNS.
  * Pokud ve virtuálním poli definujete více síťových rozhraní, může Cloud dosáhnout jenom první síťové rozhraní (ve výchozím nastavení je to **Ethernet**). Chcete-li řídit typ provozu, můžete vytvořit několik virtuálních síťových rozhraní ve virtuálním poli (nakonfigurovaných jako server iSCSI) a propojit tato rozhraní s různými podsítěmi.
* Pokud chcete omezit jenom šířku pásma cloudu (používané virtuálním polem), nakonfigurujte omezování směrovače nebo brány firewall. Pokud v hypervisoru definujete omezení, omezí se tím všechny protokoly, včetně iSCSI a protokolu SMB, nikoli jenom šířky pásma cloudu.
* Ujistěte se, že je povolená synchronizace času pro hypervisory. Pokud používáte Hyper-V, vyberte své virtuální pole ve Správci technologie Hyper-V, klikněte na **nastavení &gt; integrační služby** a ujistěte se, že je zaškrtnutá **synchronizace času** .

### <a name="storage-accounts"></a>Účty úložiště
Virtuální pole StorSimple může být přidruženo k jednomu účtu úložiště. Tento účet úložiště může být automaticky generovaný účet úložiště, účet ve stejném předplatném jako služba nebo účet úložiště, který se vztahuje k jinému předplatnému. Další informace najdete v tématu [Správa účtů úložiště pro virtuální pole](storsimple-virtual-array-manage-storage-accounts.md).

Pro účty úložiště přidružené k virtuálnímu poli použijte následující doporučení.

* Při propojování více virtuálních polí s jedním účtem úložiště je faktor v maximální kapacitě (64 TB) pro virtuální pole a maximální velikost (500 TB) pro účet úložiště. Tím se omezí počet virtuálních polí v plném velikosti, která se dají přidružit k tomuto účtu úložiště přibližně 7.
* Při vytváření nového účtu úložiště
  
  * Doporučujeme, abyste ho vytvořili v oblasti nejbližší vzdálenému Office nebo pobočce, kde je vaše virtuální pole StorSimple nasazené, aby se minimalizovala latence.
  * Pamatujte na to, že účet úložiště nemůžete přesouvat mezi různými oblastmi. Nemůžete také přesunout službu mezi předplatnými.
  * Použijte účet úložiště, který implementuje redundanci mezi datovými centry. Služba Geo-Redundant Storage (GRS), zóna redundantního úložiště (ZRS) a místně redundantní úložiště (LRS) jsou podporovány pro použití s virtuálním polem. Další informace o různých typech účtů úložiště najdete v části [replikace úložiště Azure](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Sdílené složky a svazky
Pro virtuální pole StorSimple můžete zřídit sdílené složky, pokud jsou nakonfigurované jako souborový server a svazky, pokud jsou nakonfigurované jako server iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazků se týkají velikosti a typu nakonfigurovaného.

#### <a name="volumeshare-size"></a>Velikost svazku nebo sdílené složky
Ve virtuálním poli můžete zřídit sdílené složky, pokud jsou nakonfigurované jako souborový server a svazky, pokud jsou nakonfigurované jako server iSCSI. Osvědčené postupy pro vytváření sdílených složek a svazků se týkají velikosti a typu nakonfigurovaného. 

Při zřizování sdílených složek nebo svazků na virtuálním zařízení Pamatujte na následující osvědčené postupy.

* Velikost souborů relativní vzhledem k zřízené velikosti vrstvených sdílených složek může ovlivnit výkon vrstvení. Práce s velkými soubory může mít za následek zpomalení vrstvy. Při práci s velkými soubory doporučujeme, aby byl největší soubor menší než 3% velikosti sdílené složky.
* Ve virtuálním poli se dá vytvořit maximálně 16 svazků/sdílených složek. Pro omezení velikosti místně připnutých a vrstvených svazků/sdílených složek vždy odkazují na [omezení virtuálních polí StorSimple](storsimple-ova-limits.md).
* Při vytváření svazku je třeba zvážit očekávanou spotřebu dat a také budoucí nárůst. Svazek nebo sdílenou složku nelze později rozbalit.
* Po vytvoření svazku nebo sdílené složky se velikost svazku nebo sdílené složky na StorSimple nedá zmenšit.
* Když při zápisu do vrstveného svazku v StorSimple dojde k překročení určité prahové hodnoty (vzhledem k místnímu prostoru vyhrazenému pro svazek), v/v se omezí. Pokud budete pokračovat v zápisu do tohoto svazku, zpomalí se výrazně v/v. I když můžete zapisovat na vrstvený svazek nad rámec zřízené kapacity (nebudeme aktivně zastavovat uživatele, který překračuje zřízenou kapacitu), zobrazí se oznámení upozorňující na to, že jste přepsali odběr. Jakmile se zobrazí výstraha, je nutné, abyste provedli nápravné míry, například odstranit data svazku (rozšíření svazků není aktuálně podporováno).
* Pro případy použití zotavení po havárii, protože počet povolených sdílených složek nebo svazků je 16 a maximální počet sdílených složek/svazků, které je možné zpracovat paralelně, je 16, počet sdílených složek nebo svazků nemá vliv na váš cíl RPO a RTO.

#### <a name="volumeshare-type"></a>Typ svazku nebo sdílení
StorSimple podporuje dva typy svazků nebo sdílených složek na základě využití: místně připnuté a vrstvený. Místně připnuté svazky nebo sdílené složky jsou silná zřízení, zatímco vrstvené svazky nebo sdílené složky se dynamicky zřídí. Po vytvoření se místně připojený svazek nebo sdílená složka nedá převést na vrstvený nebo *naopak* .

Při konfiguraci svazků nebo sdílených složek StorSimple doporučujeme implementovat tyto osvědčené postupy:

* Určete typ svazku založený na úlohách, které máte v úmyslu nasadit před vytvořením svazku. Používejte místně připnuté svazky pro úlohy, které vyžadují místní záruky dat (dokonce i při výpadku cloudu) a které vyžadují nízkou latenci v cloudu. Po vytvoření svazku ve virtuálním poli nemůžete změnit typ svazku z místně připnutého na vrstvený nebo *naopak*. Jako příklad vytvořte místně připojené svazky při nasazování úloh SQL nebo úloh hostujících virtuální počítače. pro úlohy sdílení souborů použijte vrstvené svazky.


#### <a name="volume-format"></a>Formát svazku
Po vytvoření svazků StorSimple na serveru iSCSI je třeba inicializovat, připojit a naformátovat svazky. Tato operace se provádí na hostiteli připojeném k vašemu zařízení StorSimple. Při připojování a formátování svazků na hostiteli StorSimple se doporučují následující osvědčené postupy.

* Proveďte rychlý formát všech StorSimple svazků.
* Při formátování svazku StorSimple použijte velikost alokační jednotky (Austrálie) 64 KB (výchozí hodnota je 4 KB). 64 KB – Austrálie vychází z interního testování pro běžné úlohy StorSimple a další úlohy.
* Při použití virtuálního pole StorSimple nakonfigurovaného jako serveru iSCSI nepoužívejte rozložené svazky ani dynamické disky, protože tyto svazky nebo disky nejsou podporované nástrojem StorSimple.

#### <a name="share-access"></a>Sdílet přístup
Při vytváření sdílených složek na souborovém serveru virtuálních polí postupujte podle těchto pokynů:

* Když vytváříte sdílenou složku, přiřaďte jako správce sdílení skupinu uživatelů místo jednoho uživatele.
* Oprávnění NTFS můžete spravovat po vytvoření sdílené složky úpravou sdílených složek v Průzkumníkovi Windows.

#### <a name="volume-access"></a>Přístup ke svazku
Když konfigurujete svazky iSCSI ve virtuálním poli StorSimple, je důležité řídit přístup všude tam, kde je to potřeba. K určení, které hostitelské servery mají přístup ke svazkům, vytvoření a přidružení záznamů řízení přístupu (záznamů ACR) se svazky StorSimple.

Při konfiguraci záznamů ACR pro StorSimple svazky použijte následující osvědčené postupy:

* Vždy přidružte aspoň jeden ACR ke svazku.

* Při přiřazování více než jednoho ACR svazku se ujistěte, že se svazek nezveřejňuje způsobem, ve kterém může být souběžně přístupný na více než jednom neclusterovaných hostitelích. Pokud jste ke svazku přiřadili více záznamů ACR, zobrazí se varovná zpráva, která vám umožní zkontrolovat vaši konfiguraci.

### <a name="data-security-and-encryption"></a>Zabezpečení a šifrování dat
Vaše virtuální pole StorSimple má funkce zabezpečení a šifrování dat, které zajišťují důvěrnost a integritu dat. Při použití těchto funkcí se doporučuje dodržovat tyto osvědčené postupy: 

* Před odesláním dat z virtuálního pole do cloudu definujte šifrovací klíč cloudového úložiště pro vygenerování šifrování AES-256. Tento klíč se nevyžaduje, pokud jsou vaše data zašifrovaná, aby začínaly na. Klíč je možné vygenerovat a udržovat v bezpečí pomocí systému správy klíčů, jako je [Azure Key trezor](../key-vault/general/overview.md).
* Při konfiguraci účtu úložiště prostřednictvím služby StorSimple Manager se ujistěte, že jste povolili režim TLS, abyste vytvořili zabezpečený kanál pro síťovou komunikaci mezi zařízením StorSimple a cloudem.
* Znovu vygenerujte klíče pro účty úložiště (přístupem ke službě Azure Storage) pravidelně Přihlaste se k jakýmkoli změnám v přístupu na základě změněného seznamu správců.
* Data ve virtuálním poli se komprimují a odstraňují před odesláním do Azure. Pro hostitele Windows serveru nedoporučujeme používat službu role odstranění duplicitních dat.

## <a name="operational-best-practices"></a>Osvědčené provozní postupy
Provozní osvědčené postupy jsou pokyny, které by měly být dodrženy během každodenního řízení nebo provozu virtuálního pole. Tyto postupy zahrnují konkrétní úlohy správy, jako je třeba pořízení záloh, obnovení ze zálohovacího skladu, provedení převzetí služeb při selhání, deaktivace a odstranění pole, monitorování využití systému a stav a spuštění kontroly virů ve virtuálním poli.

### <a name="backups"></a>Zálohování
Data ve virtuálním poli se zálohují do cloudu dvěma způsoby, což je výchozí automatizovaná denní záloha celého zařízení od 22:30 nebo přes ruční zálohování na vyžádání. Ve výchozím nastavení zařízení automaticky vytvoří denní cloudové snímky všech dat, která jsou v něm umístěná. Další informace najdete v [poznámkách k zálohování virtuálního pole StorSimple](storsimple-virtual-array-backup.md).

Frekvence a uchovávání přidružených k výchozím zálohám nelze změnit, ale můžete nastavit čas, kdy se denní zálohy mají každý den iniciovat. Při konfiguraci počátečního času pro automatizované zálohování doporučujeme:

* Naplánujte zálohování pro dobu mimo špičku. Čas zahájení zálohování by neměl odpovídat množství vstupně-výstupních operací hostitele.
* Spusťte ruční zálohování na vyžádání, když naplánujete provést převzetí služeb při selhání zařízení nebo před časovým obdobím údržby, aby se chránila data ve virtuálním poli.

### <a name="restore"></a>Obnovení
Obnovení ze zálohovacího skladu můžete provést dvěma způsoby: obnovení na jiný svazek nebo sdílenou složku nebo provedení obnovení na úrovni položek (dostupné pouze ve virtuálním poli nakonfigurovaném jako souborový server). Obnovení na úrovni položek umožňuje provádět podrobné obnovení souborů a složek z cloudové zálohy všech sdílených složek na zařízení StorSimple. Pokud potřebujete další informace, přečtěte si [obnovení ze zálohy](storsimple-virtual-array-clone.md).

Při provádění obnovení mějte na paměti následující pokyny:

* Vaše virtuální pole StorSimple nepodporuje místní obnovení. To může být možné snadno dosáhnout procesem se dvěma kroky: Uvolněte místo ve virtuálním poli a pak ho obnovte na jiný svazek nebo sdílenou složku.
* Při obnovení z místního svazku mějte na paměti, že obnovení bude dlouhodobě spuštěná operace. I když se svazek rychle doplní do online režimu, data se budou na pozadí dál vycházet.
* Typ svazku zůstává během procesu obnovení stejný. Vrstvený svazek je obnoven na jiný vrstvený svazek a místně připojený svazek k jinému místně připnutému svazku.
* Pokud se pokusíte obnovit svazek nebo sdílenou složku ze zálohovacího skladu, může se stát, že se na portálu stále vytvoří cílový svazek nebo sdílená složka. Je důležité odstranit tento nevyužitý cílový svazek nebo sdílenou složku na portálu, abyste minimalizovali případné budoucí problémy vyplývající z tohoto prvku.

### <a name="failover-and-disaster-recovery"></a>Převzetí služeb při selhání a zotavení po havárii
Převzetí služeb zařízení při selhání umožňuje migrovat data ze *zdrojového* zařízení v datacentru do jiného *cílového* zařízení umístěného ve stejném nebo jiném geografickém umístění. Převzetí služeb při selhání zařízení je pro celé zařízení. Během převzetí služeb při selhání změní cloudová data pro zdrojové zařízení vlastnictví cílového zařízení.

Pro virtuální pole StorSimple můžete převzít služby při selhání jenom do jiného virtuálního pole spravovaného stejnou službou StorSimple Manager. Převzetí služeb při selhání zařízení řady 8000 nebo pole spravované jinou StorSimple Manager službou (než u zdrojového zařízení) není povoleno. Další informace o požadavcích na převzetí služeb při selhání najdete v článku [požadavky pro převzetí služeb při selhání zařízení](storsimple-virtual-array-failover-dr.md).

Při převzetí služeb při selhání pro virtuální pole mějte na paměti následující skutečnosti:

* U plánovaného převzetí služeb při selhání se doporučuje před zahájením převzetí služeb při selhání převést všechny svazky nebo sdílené složky do režimu offline. Postupujte podle pokynů pro konkrétní operační systém a převeďte svazky nebo sdílené složky na hostiteli jako první a pak tyto počítače převeďte do svého virtuálního zařízení do offline režimu.
* V případě zotavení po havárii souborového serveru (DR) doporučujeme připojit cílové zařízení ke stejné doméně jako zdroj, aby se oprávnění ke sdílení automaticky vyřešila. V této verzi se podporuje jenom převzetí služeb při selhání u cílového zařízení ve stejné doméně.
* Po úspěšném dokončení programu DR se zdrojové zařízení automaticky odstraní. I když už zařízení není dostupné, virtuální počítač, který jste zřídili v hostitelském systému, pořád spotřebovává prostředky. Doporučujeme odstranit tento virtuální počítač z hostitelského systému, aby se předešlo případným poplatkům za časové období.
* Mějte na paměti, že i když převzetí služeb při selhání neproběhlo úspěšně, **data jsou v cloudu vždycky v bezpečí**. Vezměte v úvahu následující tři scénáře, ve kterých se převzetí služeb při selhání úspěšně nedokončilo:
  
  * Došlo k chybě v počátečních fázích převzetí služeb při selhání, například při provádění předběžných kontrol systému zotavení po havárii. V takovém případě je vaše cílové zařízení stále použitelné. Převzetí služeb při selhání můžete opakovat na stejném cílovém zařízení.
  * Během skutečného procesu převzetí služeb při selhání došlo k chybě. V takovém případě je cílové zařízení označeno jako nepoužitelné. Musíte zřídit a nakonfigurovat jiné cílové virtuální pole a použít ho pro převzetí služeb při selhání.
  * Převzetí služeb při selhání mělo za cíl, který odstranil zdrojové zařízení, ale cílové zařízení má problémy a nemůžete získat přístup k žádným datům. Data jsou stále v cloudu v bezpečí a je možné je snadno načíst vytvořením jiného virtuálního pole a jeho použitím jako cílového zařízení pro zotavení po havárii.

### <a name="deactivate"></a>Deaktivovat
Když deaktivujete virtuální pole StorSimple, nakonfigurujete připojení mezi zařízením a odpovídající službou StorSimple Manager. Deaktivace je **trvalá** operace a nelze ji vrátit zpět. Deaktivované zařízení se nedá znovu zaregistrovat u služby StorSimple Manager. Další informace získáte, když přejdete na [deaktivovat a odstraníte virtuální pole StorSimple](storsimple-virtual-array-deactivate-and-delete-device.md).

Při deaktivaci virtuálního pole mějte na paměti následující osvědčené postupy:

* Před deaktivací virtuálního zařízení si pořídit snímek všech dat v cloudu. Když deaktivujete virtuální pole, ztratí se všechna data místního zařízení. Pořízení snímku v cloudu vám umožní obnovit data v pozdější fázi.
* Před deaktivací StorSimple virtuálního pole nezapomeňte zastavit nebo odstranit klienty a hostitele, kteří na tomto zařízení závisejí.
* Odstraňte deaktivované zařízení, pokud už se nepoužíváte, takže se neúčtují poplatky.

### <a name="monitoring"></a>Monitorování
Aby se zajistilo, že vaše virtuální pole StorSimple je v nepřetržitém stavu, budete muset monitorovat pole a zajistit, aby se zobrazovaly informace ze systému včetně výstrah. Chcete-li monitorovat celkový stav virtuálního pole, implementujte tyto osvědčené postupy:

* Nakonfigurujte monitorování a sledujte využití disku s datovým diskem virtuálního pole i disk s operačním systémem. Pokud používáte Hyper-V, můžete k monitorování hostitelů virtualizace použít kombinaci System Center Virtual Machine Manager (SCVMM) a System Center Operations Manager.
* Nakonfigurujte e-mailová oznámení na virtuálním poli, abyste mohli odesílat výstrahy na určitých úrovních využití.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Hledání v indexu a aplikace pro kontrolu virů
Virtuální pole StorSimple může automaticky vrstvit data z místní vrstvy do cloudu Microsoft Azure. Když se při prohledávání dat uložených v StorSimple používá aplikace, jako je například vyhledávání v indexu nebo Kontrola virů, je nutné zajistit, aby data v cloudu nezískala přístup a zpět do místní úrovně.

Doporučujeme, abyste při konfiguraci vyhledávání v indexu nebo vyhledávání virů na virtuálním poli implementovali následující osvědčené postupy:

* Zakažte všechny automaticky nakonfigurované operace úplného prohledávání.
* V případě vrstvených svazků nakonfigurujte hledání v indexu nebo aplikaci pro kontrolu virů, aby provedlo přírůstkové prověřování. To by kontrolovalo pouze nová data, která se nejspíš nacházejí v místní úrovni. Data, která jsou vrstvena do cloudu, nejsou během přírůstkové operace k dispozici.
* Přesvědčte se, zda jsou nakonfigurovány správné filtry a nastavení vyhledávání, aby bylo možné zkontrolovat pouze zamýšlené typy souborů. Například soubory obrázků (JPEG, GIF a TIFF) a technické výkresy by neměly být prohledávány v rámci přírůstkového nebo úplného opětovného sestavení indexu.

Pokud používáte proces indexování systému Windows, postupujte podle těchto pokynů:

* Nepoužívejte indexer Windows pro vrstvené svazky, protože revolá velké objemy dat (TBs) z cloudu, pokud je potřeba index znovu sestavit často. Při opakovaném sestavování indexu by se načetly všechny typy souborů, které indexují svůj obsah.
* Použijte proces indexování Windows pro místně připnuté svazky, protože by k vytváření indexu měli přístup jenom data na místních vrstvách (data v cloudu nebudou přístupné).

### <a name="byte-range-locking"></a>Uzamykání rozsahu bajtů
Aplikace můžou uzamknout zadaný rozsah bajtů v rámci souborů. Pokud je v aplikacích, které zapisují do StorSimple, povolené uzamykání rozsahu bajtů, pak na virtuální pole nefunguje vrstva. Aby bylo možné pracovat s vrstvou, měly by být odemčeny všechny oblasti souborů, které jsou k dispozici. Uzamykání rozsahu bajtů není pro vrstvené svazky ve virtuálním poli podporováno.

Doporučené míry pro zmírnění uzamykání rozsahu bajtů zahrnují:

* Vypněte uzamykání rozsahu bajtů v logice aplikace.
* Pro data přidružená k této aplikaci použijte místně připnuté svazky (místo vrstev). Místně připnuté svazky nepatří do cloudu.
* Pokud používáte místně připojené svazky s povoleným zámkem rozsahu bajtů, může být svazek online, než se obnovení dokončí. V těchto případech musíte počkat, až se obnovení dokončí.

## <a name="multiple-arrays"></a>Více polí
Pro rostoucí pracovní sadu dat, která by mohla přecházet do cloudu, může být potřeba nasadit víc virtuálních polí, což by mělo vliv na výkon zařízení. V těchto případech je nejlepší škálovat zařízení při zvětšování pracovní sady. To vyžaduje, aby v místním datovém centru bylo přidáno jedno nebo více zařízení. Při přidávání zařízení můžete:

* Rozdělí aktuální sadu dat.
* Nasaďte nové úlohy do nových zařízení.
* Pokud nasazujete více virtuálních polí, doporučujeme, abyste z perspektivy vyrovnávání zatížení rozšíříte pole napříč různými hostiteli hypervisoru.
* V oboru názvů systém souborů DFS (Distributed File System) lze nasadit více virtuálních polí (Pokud je nakonfigurovaná jako souborový server nebo server iSCSI). Podrobný postup najdete v [systém souborů DFS (Distributed File System) řešení oboru názvů pomocí Průvodce nasazením hybridního cloudového úložiště](https://www.microsoft.com/download/details.aspx?id=45507). Replikace systém souborů DFS (Distributed File System) se momentálně nedoporučuje pro použití s virtuálním polem. 

## <a name="see-also"></a>Viz také
Naučte se [spravovat virtuální pole StorSimple](storsimple-virtual-array-manager-service-administration.md) prostřednictvím služby StorSimple Manager.