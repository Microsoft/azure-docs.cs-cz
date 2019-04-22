---
title: Nastavení zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 13c86a38e0d894feed0d9c24dd802a09ff1d1d2d
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678835"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Nastavení zotavení po havárii pro virtuální počítače Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z místních počítačů a Azure virtual machines (VM).

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítače Azure je replikace z jedné oblasti Azure do jiné. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Ověření nastavení cílových prostředků
> * Nastavení odchozího přístupu pro virtuální počítače
> * Povolit replikaci virtuálního počítače

> [!NOTE]
> Tento článek obsahuje pokyny pro nasazení zotavení po havárii pomocí nejjednodušší nastavení. Pokud chcete další informace o vlastním nastavením, projděte si články v části [části](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Ujistěte se, že rozumíte [komponentám a architektuře řešení](concepts-azure-to-azure-architecture.md).
- Zkontrolujte [požadavky na podporu](site-recovery-support-matrix-azure-to-azure.md) před zahájením.

## <a name="create-a-vault"></a>Vytvoření trezoru

V libovolné oblasti (s výjimkou zdrojové oblasti) vytvořte trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na tlačítko **vytvořit prostředek** > **nástroje pro správu** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název pro identifikaci trezoru. Pokud máte více předplatných, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků nebo vyberte existující. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Pokud chcete mít možnost rychle se dostat k trezoru z řídicího panelu, klikněte na **Připnout na řídicí panel** a potom klikněte na **Vytvořit**.

   ![Nový trezor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.

## <a name="verify-target-resources"></a>Ověření cílových prostředků

1. Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti. O povolení požadované kvóty požádejte podporu.
2. Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu velikosti virtuálních počítačů, které odpovídají velikostem zdrojových virtuálních počítačů. Služba Site Recovery vybere stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální počítač.

## <a name="configure-outbound-network-connectivity"></a>Konfigurace odchozího síťového připojení

Site Recovery k fungovat podle očekávání budete muset upravit odchozího síťového připojení z virtuálních počítačů, které chcete replikovat.

> [!NOTE]
> Site Recovery nepodporuje řízení síťového připojení pomocí proxy služby ověřování.


### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall na základě adresy URL, můžete povolte přístup k těmto adresám URL.

| **Adresa URL** | **Podrobnosti** |
| ------- | ----------- |
| *.blob.core.windows.net | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| login.microsoftonline.com | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| *.servicebus.windows.net | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud chcete řídit odchozí připojení pomocí IP adres místo adres URL, povolte tyto adresy pro brány firewall založené na protokolu IP, proxy serveru nebo pravidla skupiny zabezpečení sítě.

  - [Rozsahy IP adres datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Rozsahy IP adres datacentra Windows Azure v Německu](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Rozsahy IP adres datacentra Windows Azure v Číně](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP adresy koncových bodů služby Site Recovery](https://aka.ms/site-recovery-public-ips)

Pokud používáte skupiny zabezpečení sítě můžete vytvořit značku služby úložiště pravidla NSG pro zdrojové oblasti. [Další informace](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Ověření certifikátů virtuálních počítačů Azure

Zkontrolujte, zda je chcete replikovat virtuální počítače obsahují nejnovější kořenové certifikáty. Pokud ne virtuální počítač nelze zaregistrovaný k Site Recovery z důvodu omezení zabezpečení.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.

## <a name="set-permissions-on-the-account"></a>Nastavení oprávnění k účtu

Azure Site Recovery poskytuje pro řízení operací správy Site Recovery tři předdefinované role.

- **Přispěvatel Site Recovery** – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace.

- **Operátor Site Recovery** – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvářet ani odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kteří můžou na pokyn vlastníků aplikací nebo správců IT provést převzetí služeb při selhání virtuálních počítačů nebo aplikací. Po vyřešení havárie může operátor zotavení po havárii znovu nastavit ochranu a navrátit služby po obnovení virtuálních počítačů.

- **Čtenář Site Recovery** – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory.

Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Povolení replikace

### <a name="select-the-source"></a>Výběr zdroje

1. V trezorech služby Recovery Services klikněte na název trezoru > **+ Replikovat**.
2. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
3. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
4. Vyberte **Zdrojové předplatné**, ve kterém jsou virtuální počítače spuštěné. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
5. Vyberte **zdrojovou skupinu prostředků**a klikněte na tlačítko **OK** uložte nastavení.

    ![Nastavení zdroje](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků nebo cloudovou službu.

1. V části **Virtuální počítače** vyberte virtuální počítače, které chcete replikovat.
2. Klikněte na **OK**.

### <a name="configure-replication-settings"></a>Konfigurace nastavení replikace

Site Recovery vytvoří výchozí nastavení a zásady replikace pro cílovou oblast. Můžete změnit nastavení podle potřeby.

1. Kliknutím na **Nastavení** zobrazte nastavení cíle a replikace.
2. Chcete-li přepsat výchozí nastavení cíle, klikněte na tlačítko **vlastní** vedle **skupinu prostředků, síť, úložiště a dostupnosti**.

   ![Konfigurace nastavení](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Přizpůsobte cílová nastavení popsané v tabulce.

    **Nastavení** | **Podrobnosti**
    --- | ---
    **Cílové předplatné** | Ve výchozím nastavení cílové předplatné je stejné jako zdrojové předplatné. Po kliknutí na Přizpůsobit můžete vybrat jiné cílové předplatné ve stejném tenantovi Azure Active Directory.
    **Cílové umístění** | Cílové oblasti používané ke zotavení po havárii.<br/><br/> Doporučujeme, aby cílové umístění odpovídalo umístění trezoru Site Recovery.
    **Cílová skupina prostředků** | Skupina prostředků v cílové oblasti, která obsahuje virtuální počítače Azure po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou skupinu prostředků s příponou asr. Umístění cílové skupiny prostředků může být libovolnou oblast s výjimkou oblasti, ve kterém je vaše zdrojové virtuální počítače hostují.
    **Cílová virtuální síť** | Síť v cílové oblasti, že jsou virtuální počítače umístěné po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou virtuální síť (a podsítě) s příponou asr.
    **Účty úložiště mezipaměti** | Site Recovery využívá účet úložiště ve zdrojové oblasti. Do tohoto účtu se odešlou změny zdrojových virtuálních počítačů před replikací o cílového umístění.<br/><br/> Pokud používáte účet úložiště mezipaměti povolena brána firewall, ujistěte se, že povolíte **Povolit důvěryhodné služby Microsoftu**. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Cílové účty úložiště (zdrojový virtuální počítač používá nespravované disky)** | Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti zrcadlící účet úložiště virtuálního počítače zdrojového nový účet úložiště.<br/><br/> Povolit **Povolit důvěryhodné služby Microsoftu** Pokud používáte účet úložiště mezipaměti povolena brána firewall.
    **Repliky spravovaných disků (Pokud zdrojový virtuální počítač používá spravované disky)** | Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti zrcadlící spravované disky zdrojového Virtuálního počítače se stejným typem úložiště (Standard nebo premium), protože zdrojový virtuální počítač spravovaný disk repliky spravovaných disků.
    **Cílové skupiny dostupnosti** | Ve výchozím nastavení Azure Site Recovery vytvoří nové dostupnosti v cílové oblasti s názvem, který má příponu "Azure Site Recovery" pro virtuální počítače součástí skupiny dostupnosti ve zdrojové oblasti. V případě, že skupina dostupnosti Azure Site Recovery vytvoří již existuje, je znovu.
    **Cílové zóny dostupnosti** | Ve výchozím nastavení Site Recovery přiřadí číslo zóny jako zdrojové oblasti v cílové oblasti, v případě, že cílové oblasti podporují zóny dostupnosti.<br/><br/> Pokud cílová oblast nepodporuje zóny dostupnosti, jsou cílové virtuální počítače ve výchozím nastavení nakonfigurované jako jedné instance.<br/><br/> Klikněte na tlačítko **vlastní** ke konfiguraci virtuálních počítačů v rámci skupiny dostupnosti v cílové oblasti.<br/><br/> Typ dostupnost (jednu instanci, set nebo dostupnost zóně dostupnosti) nelze změnit po povolení replikace. Budete muset zakázat a povolit replikaci, chcete-li změnit typ dostupnosti.

4. Chcete-li přizpůsobit nastavení zásad replikace, klikněte na tlačítko **vlastní** vedle **zásady replikace**a upravte nastavení podle potřeby.

    **Nastavení** | **Podrobnosti**
    --- | ---
    **Název zásady replikace** | Název zásady.
    **Uchování bodu obnovení** | Ve výchozím nastavení uchovává Site Recovery body obnovení po dobu 24 hodin. Můžete nakonfigurovat hodnotu v rozmezí 1 až 72 hodin.
    **Frekvence pořizování snímků konzistentních s aplikací** | Ve výchozím nastavení Site Recovery pořídí konzistentního snímku každé 4 hodiny. Můžete nakonfigurovat jakoukoli hodnotu v rozmezí 1 až 12 hodin.<br/><br/> Konzistentní vzhledem k snímku je bodu v čase snímek dat aplikací ve virtuálním počítači. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači budou při pořízení snímku v konzistentním stavu.
    **Replikační skupiny** | Pokud vaše aplikace potřebuje konzistenci napříč virtuálními počítači, můžete vytvořit replikační skupinu. u těchto počítačů. Ve výchozím nastavení nejsou vybrané virtuální počítače součástí žádné replikační skupiny.

5. V **vlastní**vyberte **Ano** konzistence více virtuálních počítačů, pokud chcete přidat virtuální počítače do nové nebo existující replikační skupiny. Pak klikněte na **OK**. 

    >[!NOTE]
    >- Všechny počítače v replikační skupině mít sdílené body obnovení konzistentní vzhledem k aplikacím a konzistentní při selhání při převzetí služeb při selhání.
    >- Povolení konzistence více virtuálních počítačů může mít vliv na výkon úloh (je intenzivní nároky na procesor). To by měla sloužit pouze v případě, že počítačích běží stejná úloha a potřebujete konzistenci napříč několika počítači.
    >- Může mít maximálně 16 virtuálních počítačů v replikační skupině.
    >- Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že neexistuje žádná brána firewall blokuje interní komunikaci mezi virtuálními počítači přes tento port.
    >- Pro Linuxové virtuální počítače v replikační skupině Ujistěte se, že v souladu s pokyny pro Linux verze ručně otevřít odchozí přenosy na portu 20004.



### <a name="configure-encryption-settings"></a>Konfigurace nastavení šifrování

Pokud zdrojový virtuální počítač má povolen Azure disk encryption (ADE), zkontrolujte nastavení.

1. Ověřte nastavení:
    - **Trezory klíčů šifrování disku**: Ve výchozím nastavení vytvoří Site Recovery na šifrovací klíče disku virtuálního počítače na zdroje s příponou "Azure Site Recovery" nové služby key vault. Pokud služby key vault už existuje, je znovu.
    - **Trezory klíčů šifrovací klíče**: Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový trezor klíčů. Název má příponou "Azure Site Recovery" a je podle klíče šifrovací klíče zdrojového virtuálního počítače. Pokud služby key vault, Site Recovery vytvoří již existuje, je znovu.

2. Klikněte na tlačítko **vlastní** vybrat vlastní trezorům klíčů.

>[!NOTE]
>Pouze virtuální počítače Azure s operačními systémy Windows a [pro šifrování s aplikací Azure AD povolené](https://aka.ms/ade-aad-app) jsou aktuálně podporovány službou Azure Site Recovery.
>

### <a name="track-replication-status"></a>Sledování stavu replikace

1. V **Nastavení** klikněte na **Aktualizovat**, abyste získali nejnovější stav.
2. Sledování průběhu a stavu následujícím způsobem:
    - Sledovat průběh **povolit ochranu** úlohy **nastavení** > **úlohy** > **úlohy Site Recovery**.
    - V části **Nastavení** > **Replikované položky** můžete zobrazit stav virtuálních počítačů a průběh počáteční replikace. Kliknutím na virtuální počítač přejdete k podrobnostem o jeho nastavení.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste pro virtuální počítač Azure nakonfigurovali zotavení po havárii. Nyní můžete zahájit zotavení po havárii ke kontrole, že převzetí služeb při selhání funguje podle očekávání.

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md)
