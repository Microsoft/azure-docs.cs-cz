---
title: Podpora fyzického zjišťování a hodnocení v Azure Migrate
description: Přečtěte si o podpoře fyzického zjišťování a hodnocení pomocí Azure Migrate zjišťování a hodnocení.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: b62160861f686c6ea5a8ebfd03d904da2ad5d80a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869442"
---
# <a name="support-matrix-for-physical-server-discovery-and-assessment"></a>Matice podpory pro zjišťování a hodnocení fyzických serverů 

Tento článek shrnuje požadavky a požadavky na podporu při vyhodnocování fyzických serverů pro migraci do Azure pomocí nástroje [Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) Tool. Pokud chcete migrovat fyzické servery do Azure, přečtěte si [matici podpora migrace](migrate-support-matrix-physical-migration.md).

Chcete-li vyhodnotit fyzické servery, vytvořte projekt a přidejte do projektu nástroj Azure Migrate: Discovery and Assessment. Po přidání nástroje nasadíte [zařízení Azure Migrate](migrate-appliance.md). Zařízení nepřetržitě zjišťuje místní servery a do Azure odesílá metadata a data o výkonu serverů. Po dokončení zjišťování budete shromažďovat zjištěné servery do skupin a spustit posouzení pro skupinu.

## <a name="limitations"></a>Omezení

**Podpora** | **Podrobnosti**
--- | ---
**Omezení hodnocení** | V jednom [projektu](migrate-support-matrix.md#project)můžete zjistit a posoudit až 35 000 fyzických serverů.
**Omezení projektu** | V předplatném Azure můžete vytvořit více projektů. Kromě fyzických serverů může projekt zahrnovat i servery na VMware a Hyper-V, a to až do mezních hodnot pro každé z nich.
**Zjišťování** | Zařízení Azure Migrate může zjistit až 1000 fyzických serverů.
**Posouzení** | Do jedné skupiny můžete přidat až 35 000 serverů.<br/><br/> V jednom posouzení můžete posoudit až 35 000 serverů.

[Přečtěte si další informace](concepts-assessment-calculation.md) o posouzení.

## <a name="physical-server-requirements"></a>Požadavky na fyzický server

**Nasazení fyzického serveru:** Fyzický server může být samostatný nebo nasazený v clusteru.

**Operační systém:** Pro všechny operační systémy Windows a Linux se dá zhodnotit migrace.

**Nastaven**

- Pro Windows servery použijte doménový účet pro servery připojené k doméně a místní účet pro servery, které nejsou připojené k doméně. Uživatelský účet by měl být přidán do těchto skupin: Remote Management Users, Performance Monitor Users a Performance Log Users.
- V případě serverů s Linuxem musíte na serverech s Linuxem, které chcete vyhledat, mít účet superuživatele. Alternativně můžete nastavit nekořenový účet s požadovanými funkcemi pomocí následujících příkazů:

**Příkaz** | **Účel**
--- | --- |
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/Fdisk <br></br> setcap CAP_DAC_READ_SEARCH + EIP/sbin/Fdisk _(Pokud není k dispozici/usr/sbin/Fdisk)_ | Shromažďování dat konfigurace disku
setcap "cap_dac_override, cap_dac_read_search, cap_fowner, cap_fsetid, cap_setuid,<br>cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin<br>cap_sys_resource, cap_audit_control, cap_setfcap = + EIP "/sbin/LVM | Shromažďování dat o výkonu disku
setcap CAP_DAC_READ_SEARCH + EIP/usr/sbin/dmidecode | Shromáždění sériového čísla systému BIOS
chmod a + r –/sys/Class/DMI/ID/product_uuid | Shromažďování identifikátorů GUID systému BIOS

## <a name="azure-migrate-appliance-requirements"></a>Požadavky zařízení Azure Migrate

Azure Migrate používá [zařízení Azure Migrate](migrate-appliance.md) ke zjišťování a hodnocení. Zařízení pro fyzické servery můžete spustit na virtuálním počítači nebo fyzickém serveru.

- Přečtěte si informace o [požadavcích na zařízení](migrate-appliance.md#appliance---physical) pro fyzické servery.
- Přečtěte si o adresách URL, které zařízení potřebuje k přístupu ve [veřejných](migrate-appliance.md#public-cloud-urls) a [státních](migrate-appliance.md#government-cloud-urls) cloudech.
- Zařízení se nastavuje pomocí [skriptu PowerShellu](how-to-set-up-appliance-physical.md) , který stáhnete z Azure Portal.
V Azure Government nasaďte zařízení [pomocí tohoto skriptu](deploy-appliance-script-government.md).

## <a name="port-access"></a>Přístup k portu

Následující tabulka shrnuje požadavky na porty pro posouzení.

**Zařízení** | **Připojení**
--- | ---
**Náplně** | Příchozí připojení na portu TCP 3389 umožňující připojení ke vzdálené ploše zařízení.<br/><br/> Příchozí připojení na portu 44368 pro vzdálený přístup k aplikaci pro správu zařízení pomocí adresy URL: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Odchozí připojení na portech 443 (HTTPS), která odesílají metadata zjišťování a výkonu Azure Migrate.
**Fyzické servery** | **Windows:** Příchozí připojení na portu WinRM 5985 (HTTP) nebo 5986 (HTTPS) k vyžádání metadat konfigurace a výkonu ze serverů Windows. <br/><br/> **Linux:**  Příchozí připojení na portu 22 (TCP) k vyžádání metadat konfigurace a výkonu ze serverů se systémem Linux. |

## <a name="agent-based-dependency-analysis-requirements"></a>Požadavky na analýzu závislostí na základě agentů

[Analýza závislostí](concepts-dependency-visualization.md) vám pomůže identifikovat závislosti mezi místními servery, které chcete vyhodnotit a migrovat do Azure. Tabulka shrnuje požadavky na nastavení analýzy závislostí založené na agentech. Pro fyzické servery je aktuálně podporována pouze analýza závislostí založená na agentech.

**Požadavek** | **Podrobnosti**
--- | ---
**Před nasazením** | Měli byste mít projekt na místě, a to pomocí nástroje Azure Migrate: Discovery and Assessment Tool přidaný do projektu.<br/><br/>  Vizualizace závislostí nasadíte po nastavení zařízení Azure Migrate pro zjišťování vašich místních serverů.<br/><br/> [Naučte](create-manage-projects.md) se, jak poprvé vytvořit projekt.<br/> [Přečtěte si, jak](how-to-assess.md) přidat nástroj pro vyhodnocení do existujícího projektu.<br/> Naučte se, jak nastavit zařízení Azure Migrate pro posouzení [technologie Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)nebo fyzických serverů.
**Azure Government** | Vizualizace závislostí není v Azure Government k dispozici.
**Log Analytics** | Azure Migrate používá řešení [Service map](../azure-monitor/vm/service-map.md) v [protokolech Azure monitor](../azure-monitor/logs/log-query-overview.md) pro vizualizaci závislostí.<br/><br/> K projektu přidružíte nový nebo existující Log Analytics pracovní prostor. Pracovní prostor pro projekt nelze po přidání změnit. <br/><br/> Pracovní prostor musí být ve stejném předplatném jako projekt.<br/><br/> Pracovní prostor se musí nacházet v oblastech Východní USA, jihovýchodní Asie nebo Západní Evropa. Pracovní prostory v jiných oblastech nelze přidružit k projektu.<br/><br/> Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> V Log Analytics je pracovní prostor přidružený k Azure Migrate označený klíčem projektu migrace a názvem projektu.
**Vyžadovaná agenti** | Na každém serveru, který chcete analyzovat, nainstalujte následující agenty:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Agent závislostí](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Pokud nejsou místní servery připojené k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.<br/><br/> Přečtěte si další informace o instalaci [agenta závislostí](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) a [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Pracovní prostor Log Analytics** | Pracovní prostor musí být ve stejném předplatném projektu.<br/><br/> Azure Migrate podporuje pracovní prostory, které jsou umístěné v oblastech Východní USA, jihovýchodní Asie a Západní Evropa.<br/><br/>  Pracovní prostor musí být v oblasti, ve které [je podporovaná Service map](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Pracovní prostor pro projekt nelze po přidání změnit.
**Náklady** | V řešení Service Map se neúčtují žádné poplatky za prvních 180 dní (od dne, kdy přiřadíte pracovní prostor Log Analytics k projektu)/<br/><br/> Po uplynutí 180 dnů se začnou účtovat standardní poplatky za Log Analytics.<br/><br/> Použití jiného řešení než Service Map v přidruženém pracovním prostoru Log Analytics bude účtovat [standardní poplatky](https://azure.microsoft.com/pricing/details/log-analytics/) za Log Analytics.<br/><br/> Po odstranění projektu se pracovní prostor neodstraní spolu s ním. Po odstranění projektu Service Map využití není volné a každý uzel se bude účtovat podle placené úrovně Log Analytics pracovního prostoru/<br/><br/>Pokud máte projekty, které jste vytvořili před Azure Migrate všeobecné dostupnosti (GA-28 února 2018), mohly by vám být účtovány další Service Map poplatky. Aby se zajistila platba jenom po 180 dnech, doporučujeme vytvořit nový projekt, protože stávající pracovní prostory před GAm jsou stále Fakturovatelné.
**správy** | Při registraci agentů do pracovního prostoru použijete ID a klíč poskytnutý projektem.<br/><br/> Pracovní prostor Log Analytics můžete použít mimo Azure Migrate.<br/><br/> Pokud odstraníte přidružený projekt, pracovní prostor se automaticky neodstraní. [Odstraňte ji ručně](../azure-monitor/logs/manage-access.md).<br/><br/> Neodstraňujte pracovní prostor vytvořený pomocí Azure Migrate, pokud projekt neodstraníte. Pokud to uděláte, funkce vizualizace závislosti nebude fungovat podle očekávání.
**Připojení k internetu** | Pokud nejsou servery připojené k Internetu, musíte na ně nainstalovat bránu Log Analytics.
**Azure Government** | Analýza závislostí založená na agentech není podporována.

## <a name="next-steps"></a>Další kroky

[Příprava na fyzické zjišťování a posouzení](./tutorial-discover-physical.md).