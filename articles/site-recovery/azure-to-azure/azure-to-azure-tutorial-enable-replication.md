---
title: Nastavení zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure pomocí Azure Site Recovery (Preview)
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: f0b5eac1ac6b682de3ae9e18fd53e8bf55acdde4
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677424"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region-preview"></a>Nastavení zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure (Preview)

Služba [Azure Site Recovery](../site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení pro místní počítače a virtuální počítače Azure.

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii do sekundární oblasti Azure pro virtuální počítače Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Ověření nastavení cílových prostředků
> * Nastavení odchozího přístupu pro virtuální počítače
> * Povolit replikaci virtuálního počítače

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](concepts-azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-azure-to-azure.md) pro všechny komponenty.

## <a name="create-a-vault"></a>Vytvoření trezoru

V libovolné oblasti (s výjimkou zdrojové oblasti) vytvořte trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název pro identifikaci trezoru. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků nebo vyberte existující. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.

## <a name="verify-target-resources"></a>Ověření cílových prostředků

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. O povolení požadované kvóty požádejte podporu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Site Recovery vybere pro cílový virtuální počítač stejnou nebo nejbližší možnou velikost.

## <a name="configure-outbound-network-connectivity"></a>Konfigurace odchozího síťového připojení

Aby služba Site Recovery fungovala podle očekávání, je potřeba provést několik změn v možnostech odchozího síťového připojení z virtuálních počítačů, které chcete replikovat.

- Site Recovery nepodporuje řízení síťového připojení pomocí ověřovacího proxy serveru.
- Pokud máte ověřovací proxy server, replikaci není možné povolit.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adresách URL, povolte přístup k následujícím adresám URL, které používá Site Recovery.

| **Adresa URL** | **Podrobnosti** |
| ------- | ----------- |
| *.blob.core.windows.net | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| login.microsoftonline.com | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| *.servicebus.windows.net | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Při použití proxy serveru, pravidel NSG nebo brány firewall založených na IP adresách k řízení odchozího připojení je potřeba přidat na seznam povolených následující rozsahy IP adres. Seznam rozsahů si můžete stáhnout na následujících odkazech:

  - [Rozsahy IP adres datacentra Microsoft Azure](http://www.microsoft.com/download/details.aspx?id=41653)
  - [Rozsahy IP adres datacentra Windows Azure v Německu](http://www.microsoft.com/download/details.aspx?id=54770)
  - [Rozsahy IP adres datacentra Windows Azure v Číně](http://www.microsoft.com/download/details.aspx?id=42064)
  - [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP adresy koncových bodů služby Site Recovery](https://aka.ms/site-recovery-public-ips)

Pomocí těchto seznamů ve své síti nakonfigurujte řízení přístupu k sítím. Požadovaná pravidla NSG můžete vytvořit pomocí tohoto [skriptu](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702).

## <a name="verify-azure-vm-certificates"></a>Ověření certifikátů virtuálních počítačů Azure

Zkontrolujte, jestli jsou na virtuálních počítačích s Windows nebo Linuxem, které chcete replikovat, všechny nejnovější kořenové certifikáty. Pokud tam nejnovější kořenové certifikáty nejsou, nepůjde příslušný virtuální počítač kvůli omezením zabezpečení zaregistrovat ve službě Site Recovery.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.

- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.

## <a name="set-permissions-on-the-account"></a>Nastavení oprávnění k účtu

Azure Site Recovery poskytuje pro řízení operací správy Site Recovery tři předdefinované role.

- **Přispěvatel Site Recovery** – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace.

- **Operátor Site Recovery** – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvářet ani odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kteří můžou na pokyn vlastníků aplikací nebo správců IT provést převzetí služeb při selhání virtuálních počítačů nebo aplikací. Po vyřešení havárie může operátor zotavení po havárii znovu nastavit ochranu a navrátit služby po obnovení virtuálních počítačů.

- **Čtenář Site Recovery** – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory.

Další informace o [předdefinovaných rolích Azure RBAC](../../active-directory/role-based-access-built-in-roles.md)

## <a name="enable-replication"></a>Povolení replikace

### <a name="select-the-source"></a>Výběr zdroje

1. V trezorech služby Recovery Services klikněte na název trezoru > **+ Replikovat**.
2. Jako **Zdroj** vyberte **Azure – PREVIEW**.
3. Jako **Zdrojové umístění** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
4. Vyberte pro virtuální počítače **Model nasazení virtuálních počítačů Azure**: **Resource Manager** nebo **Classic**.
5. Vyberte **zdrojovou skupinu prostředků** pro virtuální počítače Resource Manageru nebo **cloudovou službu** pro virtuální počítače Classic.
6. Kliknutím na **OK** uložte nastavení.

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků nebo cloudovou službu.

1. V části **Virtuální počítače** vyberte virtuální počítače, které chcete replikovat.
2. Klikněte na **OK**.

### <a name="configure-replication-settings"></a>Konfigurace nastavení replikace

Site Recovery vytvoří výchozí nastavení a zásady replikace pro cílovou oblast. Nastavení můžete na základě vašich požadavků změnit.

1. Klikněte na tlačítko **nastavení** zobrazíte nastavení cíle.
2. Chcete-li přepsat výchozí nastavení cíl, klikněte na tlačítko **přizpůsobit**. 

![Konfigurace nastavení](./media/azure-to-azure-tutorial-enable-replication/settings.png)


- **Cílové umístění:** Cílové umístění, které se použije pro zotavení po havárii. Doporučujeme, aby cílové umístění odpovídalo umístění trezoru Site Recovery.

- **Cílová skupina prostředků:** Skupina prostředků v cílové oblasti, která bude obsahovat virtuální počítače Azure po převzetí služeb při selhání. Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou skupinu prostředků s příponou asr.

- **Cílová virtuální síť:** Síť v cílové oblasti, ve které budou virtuální počítače po převzetí služeb při selhání.
  Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou virtuální síť (a podsítě) s příponou asr.

- **Účty úložiště mezipaměti:** Site Recovery používá účet úložiště ve zdrojové oblasti. Do tohoto účtu se odešlou změny zdrojových virtuálních počítačů před replikací o cílového umístění.

- **Cílové účty úložiště:** Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový účet úložiště zrcadlící účet úložiště zdrojového virtuálního počítače.

- **Cílové skupiny dostupnosti:** Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou skupinu dostupnosti s příponou asr. Skupiny dostupnosti můžete přidávat pouze v případě, že jsou virtuální počítače ve zdrojové oblasti součástí sady.

- **Název zásady replikace:** Název zásady.

- **Uchovávání bodu obnovení:** Ve výchozím nastavení uchovává Site Recovery body obnovení po dobu 24 hodin. Můžete nakonfigurovat hodnotu v rozmezí 1 až 72 hodin.

- **Frekvence pořizování snímků konzistentních vzhledem k aplikacím:** Ve výchozím nastavení pořizuje Site Recovery snímek konzistentní vzhledem k aplikacím každé 4 hodiny. Můžete nakonfigurovat jakoukoli hodnotu v rozmezí 1 až 12 hodin. Snímek konzistentní vzhledem k aplikacím je snímek dat aplikací ve virtuálním počítači v daném okamžiku. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači budou při pořízení snímku v konzistentním stavu.

### <a name="track-replication-status"></a>Sledování stavu replikace

1. V **Nastavení** klikněte na **Aktualizovat**, abyste získali nejnovější stav.

2. Průběh úlohy **Povolení ochrany** můžete sledovat v části **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.

3. V části **Nastavení** > **Replikované položky** můžete zobrazit stav virtuálních počítačů a průběh počáteční replikace. Kliknutím na virtuální počítač přejdete k podrobnostem o jeho nastavení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali zotavení po havárii pro virtuální počítač Azure. Dalším krokem je test vaší konfigurace.

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md)
