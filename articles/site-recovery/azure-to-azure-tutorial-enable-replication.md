---
title: Nastavení zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 0aa7b7f3558bab7f3553527e03c44d71dd5a87ac
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833138"
---
# <a name="set-up-disaster-recovery-for-azure-vms-to-a-secondary-azure-region"></a>Nastavení zotavení po havárii pro virtuální počítače Azure do sekundární oblasti Azure

[Azure Site Recovery](site-recovery-overview.md) služby přispívá ke strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení z místních počítačů a Azure virtual machines (VM).

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii do sekundární oblasti Azure pro virtuální počítače Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Ověření nastavení cílových prostředků
> * Nastavení odchozího přístupu pro virtuální počítače
> * Povolit replikaci virtuálního počítače

> [!NOTE]
> Tento článek obsahuje pokyny pro nasazení zotavení po havárii pomocí nejjednodušší nastavení. Pokud chcete další informace o vlastním nastavením, projděte si články v části [části](azure-to-azure-how-to-enable-replication.md). O

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

  - [Rozsahy IP adres datacentra Microsoft Azure](https://www.microsoft.com/en-us/download/details.aspx?id=41653)
  - [Rozsahy IP adres datacentra Windows Azure v Německu](https://www.microsoft.com/en-us/download/details.aspx?id=54770)
  - [Rozsahy IP adres datacentra Windows Azure v Číně](https://www.microsoft.com/en-us/download/details.aspx?id=42064)
  - [Adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [IP adresy koncových bodů služby Site Recovery](https://aka.ms/site-recovery-public-ips)

To může být použito [skript](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) vytvoření požadovaná pravidla NSG.

## <a name="verify-azure-vm-certificates"></a>Ověření certifikátů virtuálních počítačů Azure

Zkontrolujte, zda je chcete replikovat virtuální počítače obsahují nejnovější kořenové certifikáty. Pokud ne virtuální počítač nelze zaregistrovaný k Site Recovery z důvodu omezení zabezpečení.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.

## <a name="set-permissions-on-the-account"></a>Nastavení oprávnění k účtu

Azure Site Recovery poskytuje pro řízení operací správy Site Recovery tři předdefinované role.

- **Přispěvatel Site Recovery** – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace.

- **Operátor Site Recovery** – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvářet ani odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kteří můžou na pokyn vlastníků aplikací nebo správců IT provést převzetí služeb při selhání virtuálních počítačů nebo aplikací. Po vyřešení havárie může operátor zotavení po havárii znovu nastavit ochranu a navrátit služby po obnovení virtuálních počítačů.

- **Čtenář Site Recovery** – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory.

Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication"></a>Povolení replikace

### <a name="select-the-source"></a>Výběr zdroje

1. V trezorech služby Recovery Services klikněte na název trezoru > **+ Replikovat**.
2. V rozevíracím seznamu **Zdroj** vyberte **Azure**.
3. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
4. Vyberte **Zdrojové předplatné**, ve kterém jsou virtuální počítače spuštěné. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
5. Vyberte **zdrojovou skupinu prostředků** pro virtuální počítače Resource Manageru nebo **cloudovou službu** pro virtuální počítače Classic.
6. Kliknutím na **OK** uložte nastavení.

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků nebo cloudovou službu.

1. V části **Virtuální počítače** vyberte virtuální počítače, které chcete replikovat.
2. Klikněte na **OK**.

### <a name="configure-replication-settings"></a>Konfigurace nastavení replikace

Site Recovery vytvoří výchozí nastavení a zásady replikace pro cílovou oblast. Můžete změnit nastavení podle potřeby.

1. Kliknutím na **Nastavení** zobrazte nastavení cíle a replikace.
2. Chcete-li přepsat výchozí nastavení cíle, klikněte na tlačítko **vlastní** vedle **skupinu prostředků, síť, úložiště a dostupnosti**.

  ![Konfigurace nastavení](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Přizpůsobte cílová nastavení následujícím způsobem:

    - **Cílové předplatné:** Cílové předplatné, které se použije pro zotavení po havárii. Ve výchozím nastavení bude cílové předplatné stejné jako zdrojové předplatné. Po kliknutí na Přizpůsobit můžete vybrat jiné cílové předplatné ve stejném tenantovi Azure Active Directory.
    - **Cílové umístění:** Cílové umístění, které se použije pro zotavení po havárii. Doporučujeme, aby cílové umístění odpovídalo umístění trezoru Site Recovery.
    - **Cílová skupina prostředků:** Skupina prostředků v cílové oblasti, která bude obsahovat virtuální počítače Azure po převzetí služeb při selhání. Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou skupinu prostředků s příponou asr. Umístěním cílové skupiny prostředků může být libovolná oblast s výjimkou oblasti, ve které jsou hostované vaše zdrojové virtuální počítače.
    - **Cílová virtuální síť:** Síť v cílové oblasti, ve které budou virtuální počítače po převzetí služeb při selhání.
      Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti novou virtuální síť (a podsítě) s příponou asr.
    - **Účty úložiště mezipaměti:** Site Recovery používá účet úložiště ve zdrojové oblasti. Do tohoto účtu se odešlou změny zdrojových virtuálních počítačů před replikací o cílového umístění.
      >[!NOTE]
      >Pokud používáte účet úložiště mezipaměti povolenou bránu firewall, ujistěte se vám "Povolit důvěryhodným službám Microsoftu". [Další informace](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Cílové účty úložiště (pokud zdrojový virtuální počítač nepoužívá spravované disky):** Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti nový účet úložiště zrcadlící účet úložiště zdrojového virtuálního počítače.
      >[!NOTE]
      >Pokud používáte účet úložiště povolenou bránu firewall zdroje nebo cíle, zajištění "Povolit důvěryhodným službám Microsoftu". [Další informace](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
      >

    - **Repliky spravovaných disků (pokud zdrojový virtuální počítač používá spravované disky):** Ve výchozím nastavení vytvoří Site Recovery v cílové oblasti repliky spravovaných disků zrcadlící spravované disky zdrojového virtuálního počítače se stejným typem úložiště (Standard nebo Premium) jako u spravovaných disků zdrojového virtuálního počítače.
    - **Cílové skupiny dostupnosti**: ve výchozím nastavení Azure Site Recovery vytvoří nové dostupnosti v cílové oblasti s názvem, který má příponu "Azure Site Recovery" pro virtuální počítače součástí skupiny dostupnosti ve zdrojové oblasti. V případě, že skupina dostupnosti Azure Site Recovery vytvoří již existuje, je znovu.
    - **Cílové zóny dostupnosti**: ve výchozím nastavení, Site Recovery přiřadí číslo zóny jako zdrojové oblasti v cílové oblasti, v případě, že cílové oblasti podporují zóny dostupnosti. 

    Pokud cílová oblast nepodporuje zóny dostupnosti, jsou cílové virtuální počítače ve výchozím nastavení nakonfigurované jako jedné instance. V případě potřeby můžete nakonfigurovat tyto virtuální počítače kliknutím na "Vlastní" jako součást skupiny dostupnosti v cílové oblasti.

    >[!NOTE]
    >Typ dostupnosti - jediné instance, set nebo dostupnost zónu dostupnosti, nelze změnit po povolení replikace. Budete muset zakázat a povolit replikaci, chcete-li změnit typ dostupnosti.
    >

4. Chcete-li přizpůsobit nastavení zásad replikace, klikněte na tlačítko **vlastní** vedle **zásady replikace**a upravte následující nastavení podle potřeby:

    - **Název zásady replikace:** Název zásady.
    - **Uchovávání bodu obnovení:** Ve výchozím nastavení uchovává Site Recovery body obnovení po dobu 24 hodin. Můžete nakonfigurovat hodnotu v rozmezí 1 až 72 hodin.
    - **Frekvence pořizování snímků konzistentních vzhledem k aplikacím:** Ve výchozím nastavení pořizuje Site Recovery snímek konzistentní vzhledem k aplikacím každé 4 hodiny. Můžete nakonfigurovat jakoukoli hodnotu v rozmezí 1 až 12 hodin. Snímek konzistentní vzhledem k aplikacím je snímek dat aplikací ve virtuálním počítači v daném okamžiku. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači budou při pořízení snímku v konzistentním stavu.
    - **Replikační skupina:** Pokud vaše aplikace vyžaduje konzistenci napříč několika virtuálními počítači, můžete pro tyto virtuální počítače vytvořit replikační skupinu. Ve výchozím nastavení nejsou vybrané virtuální počítače součástí žádné replikační skupiny.

5. V **vlastní**vyberte **Ano** konzistence více virtuálních počítačů, pokud chcete přidat virtuální počítače do nové nebo existující replikační skupiny. Chcete-li virtuální počítače součástí replikační skupiny. Pak klikněte na **OK**.

    - Všechny počítače v replikační skupině budou mít v případě převzetí služeb při selhání sdílené body obnovení konzistentní pro případ chyby a konzistentní vzhledem k aplikacím. Povolení konzistence napříč několika virtuálními počítači může mít vliv na výkon úloh a mělo by se použít pouze v případě, že je na počítačích spuštěná stejná úloha a potřebujete konzistenci napříč několika počítači.
    - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že žádné zařízení brány firewall neblokuje interní komunikaci mezi virtuálními počítači přes port 20004. Pokud chcete do replikační skupiny zahrnout virtuální počítače s Linuxem, nezapomeňte ručně otevřít odchozí provoz na portu 20004 podle pokynů ke konkrétní verzi Linuxu.

### <a name="configure-encryption-settings"></a>Konfigurace nastavení šifrování

Pokud zdrojový virtuální počítač má povolen Azure disk encryption (ADE), se zobrazí nastavení šifrování:

1. Zkontrolujte nastavení šifrování.
    - **Trezory klíčů pro šifrování disků:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčů pro šifrování disků zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor.
    - **Trezory klíčů pro šifrování klíčů:** Azure Site Recovery ve výchozím nastavení vytvoří v cílové oblasti nový trezor klíčů s příponou názvu „asr“ na základě klíčových šifrovacích klíčů zdrojového virtuálního počítače. Pokud již existuje trezor klíčů vytvořený službou Azure Site Recovery, použije se tento trezor.

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
