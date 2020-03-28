---
title: Nastavení zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292488"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Nastavení zotavení po havárii pro virtuální počítače Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zotavení po havárii tím, že spravuje a organizuje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure.

Tento kurz ukazuje, jak nastavit zotavení po havárii pro virtuální počítače Azure replikací z jedné oblasti Azure do jiné. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Ověření nastavení cílových prostředků
> * Nastavení odchozího připojení k síti pro virtuální hospovrty
> * Povolení replikace virtuálního počítače

> [!NOTE]
> Tento článek obsahuje pokyny pro nasazení zotavení po havárii s nejjednodušší nastavení. Pokud se chcete dozvědět o přizpůsobených nastaveních, přečtěte si články v [části Jak na to](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Prostudujte si [architekturu a komponenty scénáře](concepts-azure-to-azure-architecture.md).
- Než začnete, zkontrolujte [požadavky na podporu.](site-recovery-support-matrix-azure-to-azure.md)

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte trezor v libovolné oblasti, s výjimkou zdrojové oblasti.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**. Potom vyberte **nástroj & nástroje pro správu** > IT zálohování a obnovení**lokality**.
1. V **poli Název**zadejte popisný název pro identifikaci trezoru. Pokud máte více předplatných, vyberte příslušné předplatné.
1. Vytvořte skupinu prostředků nebo vyberte existující. Zadejte oblast Azure. Pokud chcete zkontrolovat podporované oblasti, přečtěte si informace o geografické dostupnosti v [tématu Podrobnosti o cenách obnovení webu Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Pokud chcete k úschovně přistupovat z řídicího panelu, vyberte **Připnout na řídicí panel** a pak vyberte **Vytvořit**.

   ![Nový trezor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.

## <a name="verify-target-resource-settings"></a>Ověření nastavení cílových prostředků

Zkontrolujte předplatné Azure pro cílovou oblast.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti. O povolení požadované kvóty požádejte podporu.
- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu velikostí virtuálních her, které odpovídají vašim zdrojovým virtuálním počítačům. Site Recovery vybere stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální počítače.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Nastavení odchozího připojení k síti pro virtuální hospovrty

Aby obnovení lokality fungovalo podle očekávání, je třeba upravit odchozí připojení k síti z virtuálních stránek, které chcete replikovat.

> [!NOTE]
> Site Recovery nepodporuje použití ověřovacího proxy serveru pro řízení připojení k síti.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Adresa URL** | **Podrobnosti** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| `login.microsoftonline.com` | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| `*.servicebus.windows.net` | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud používáte skupinu zabezpečení sítě (NSG), vytvořte pravidla skupiny zabezpečení na základě značek služeb pro přístup k Azure Storage, Azure Active Directory, službě Site Recovery a monitorování obnovení webu. [Další informace](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Ověření certifikátů virtuálních počítačů Azure

Zkontrolujte, zda virtuální chody, které chcete replikovat, mají nejnovější kořenové certifikáty. Pokud tomu tak není, virtuální ho virtuálního zabezpečovacího programu nelze zaregistrovat na site recovery z důvodu omezení zabezpečení.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.

## <a name="set-permissions-on-the-account"></a>Nastavení oprávnění k účtu

Azure Site Recovery poskytuje pro řízení operací správy Site Recovery tři předdefinované role.

- **Přispěvatel Site Recovery** – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace.

- **Operátor Site Recovery** – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvářet ani odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kteří můžou na pokyn vlastníků aplikací nebo správců IT provést převzetí služeb při selhání virtuálních počítačů nebo aplikací. Po vyřešení katastrofy může operátor zotavení po havárii znovu chránit a navrácení služeb po obnovení virtuálních počítačů.

- **Čtenář Site Recovery** – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory.

Další informace o [předdefinovaných rolích Azure RBAC](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Povolení replikace virtuálního počítače

Následující části popisují, jak povolit replikaci.

### <a name="select-the-source"></a>Výběr zdroje

Chcete-li zahájit nastavení replikace, zvolte zdroj, ve kterém jsou spuštěné virtuální počítače Azure.

1. Přejděte do **trezorů služby Recovery Services**, vyberte název úschovny a vyberte **možnost +Replikovat**.
1. Pro **zdroj**vyberte **Azure**.
1. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
1. Vyberte **Zdrojové předplatné**, ve kterém jsou virtuální počítače spuštěné. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
1. Vyberte **skupinu zdrojových prostředků**a výběrem **možnosti OK** uložte nastavení.

   ![Nastavení zdroje](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků nebo cloudovou službu.

1. V části **Virtuální počítače** vyberte virtuální počítače, které chcete replikovat.
1. Vyberte **OK**.

### <a name="configure-replication-settings"></a>Konfigurace nastavení replikace

Site Recovery vytvoří výchozí nastavení a zásady replikace pro cílovou oblast. Tato nastavení můžete podle potřeby změnit.

1. Výběrem **možnosti Nastavení** zobrazíte nastavení cíle a replikace.

1. Chcete-li přepsat výchozí nastavení cíle, vyberte **možnost Přizpůsobit** vedle **skupiny prostředků, sítě, úložiště a dostupnosti**.

   ![Konfigurace nastavení](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Přizpůsobte nastavení cílů tak, jak je shrnuto v tabulce.

   | **Nastavení** | **Podrobnosti** |
   | --- | --- |
   | **Cílové předplatné** | Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné. Vyberte **přizpůsobit** a vyberte jiné cílové předplatné v rámci stejného klienta Služby Azure Active Directory. |
   | **Cílové umístění** | Cílová oblast používaná pro zotavení po havárii.<br/><br/> Doporučujeme, aby cílové umístění odpovídalo umístění trezoru Site Recovery. |
   | **Cílová skupina prostředků** | Skupina prostředků v cílové oblasti, která obsahuje virtuální počítače Azure po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení site recovery vytvoří novou skupinu `asr` prostředků v cílové oblasti s příponou. Umístění cílové skupiny prostředků může být libovolná oblast s výjimkou oblasti, ve které jsou hostované zdrojové virtuální počítače. |
   | **Cílová virtuální síť** | Síť v cílové oblasti, která virtuální chody jsou umístěny po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení site recovery vytvoří novou virtuální síť (a podsítě) v cílové oblasti s příponou. `asr` |
   | **Účty úložiště mezipaměti** | Site Recovery používá účet úložiště ve zdrojové oblasti. Do tohoto účtu se odešlou změny zdrojových virtuálních počítačů před replikací o cílového umístění.<br/><br/> Pokud používáte účet úložiště mezipaměti s povolenou bránou firewall, ujistěte se, že **povolujete povolit důvěryhodné služby společnosti Microsoft**. [Další informace](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Také se ujistěte, že povolíte přístup alespoň k jedné podsíti zdrojové virtuální sítě. |
   | **Cílové účty úložiště (zdrojový virtuální počítače používá nespravované disky)** | Ve výchozím nastavení site recovery vytvoří nový účet úložiště v cílové oblasti zrcadlit účet úložiště zdrojového virtuálního zařízení.<br/><br/> Povolte **povolit důvěryhodné služby společnosti Microsoft,** pokud používáte účet úložiště mezipaměti s povolenou bránou firewall. |
   | **Replika spravovaných disků (Pokud zdrojový virtuální počítače používá spravované disky)** | Ve výchozím nastavení site recovery vytvoří repliky spravované disky v cílové oblasti zrcadlit zdrojové virtuální počítače spravované disky se stejným typem úložiště (standardní nebo premium) jako zdrojový virtuální hod spravovaný disk. Typ disku lze přizpůsobit pouze. |
   | **Cílové skupiny dostupnosti** | Ve výchozím nastavení Azure Site Recovery vytvoří novou sadu `asr` dostupnosti v cílové oblasti s názvem s příponou pro virtuální počítače část dostupnosti sady ve zdrojové oblasti. V případě, že sada dostupnosti vytvořená službou Azure Site Recovery už existuje, je znovu použitá. |
   | **Cílové zóny dostupnosti** | Ve výchozím nastavení site recovery přiřadí stejné číslo zóny jako zdrojová oblast v cílové oblasti, pokud cílová oblast podporuje zóny dostupnosti.<br/><br/> Pokud cílová oblast nepodporuje zóny dostupnosti, cílové virtuální počítače jsou ve výchozím nastavení nakonfigurovány jako jednotlivé instance.<br/><br/> Vyberte **Přizpůsobit** a nakonfigurujete virtuální počítače jako součást dostupnosti v cílové oblasti.<br/><br/> Po povolení replikace nelze změnit typ dostupnosti (jedna instance, sada dostupnosti nebo zóna dostupnosti). Chcete-li změnit typ dostupnosti, zakažte a povolte replikaci. |

1. Chcete-li přizpůsobit nastavení zásad replikace, vyberte **možnost Přizpůsobit** vedle **zásad replikace**a podle potřeby upravte nastavení.

   | **Nastavení** | **Podrobnosti** |
   | --- | --- |
   | **Název zásadreplikace** | Název zásady. |
   | **Uchování bodu obnovení** | Ve výchozím nastavení site recovery udržuje body obnovení po dobu 24 hodin. Můžete nakonfigurovat hodnotu v rozmezí 1 až 72 hodin. |
   | **Frekvence snímků konzistentních s aplikací** | Ve výchozím nastavení site recovery trvá snímek konzistentní s aplikací každé 4 hodiny. Můžete nakonfigurovat jakoukoli hodnotu v rozmezí 1 až 12 hodin.<br/><br/> Snímek konzistentní s aplikací je snímek dat aplikace v čase uvnitř virtuálního počítače. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači budou při pořízení snímku v konzistentním stavu. |
   | **Replikační skupina** | Pokud vaše aplikace potřebuje konzistenci více virtuálních počítačů mezi virtuálními počítači, můžete pro tyto virtuální počítače vytvořit replikační skupinu. Ve výchozím nastavení nejsou vybrané virtuální počítače součástí žádné replikační skupiny. |

1. V **části Vlastní nastavení**vyberte **Ano** pro konzistenci více virtuálních lidí, pokud chcete přidat virtuální virtuální aplikace do nové nebo existující replikační skupiny. Pak vyberte **OK**.

   > [!NOTE]
   > - Všechny počítače v replikační skupině mají sdílené chyby konzistentní a konzistentní aplikace body obnovení při převzetí služby při selhání.
   > - Povolení konzistence více virtuálních počítače může mít vliv na výkon pracovního vytížení (je náročné na procesor). Měl by se používat pouze v případě, že počítače jsou spuštěny stejné úlohy a potřebujete konzistenci mezi více počítačů.
   > - V replikační skupině můžete mít maximálně 16 virtuálních<-ti.
   > - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že neexistuje žádný firewall blokuje vnitřní komunikaci mezi virtuálními počítačemi přes tento port.
   > - U virtuálních počítačů s Linuxem v replikační skupině zajistěte, aby se odchozí provoz na portu 20004 ručně otevřel v souladu s pokyny pro linuxovou verzi.

### <a name="configure-encryption-settings"></a>Konfigurace nastavení šifrování

Pokud má zdrojový virtuální počítač povoleno šifrování disku Azure (ADE), zkontrolujte nastavení.

1. Ověřte nastavení:
   1. **Trezory šifrovacích klíčů disku**: Ve výchozím nastavení site recovery vytvoří `asr` nový trezor klíčů na zdrojových šifrovacích klíčích disku virtuálního počítače s příponou. Pokud trezor klíčů již existuje, je znovu použit.
   1. **Trezory šifrovacích klíčů**: Ve výchozím nastavení site recovery vytvoří nový trezor klíčů v cílové oblasti. Název má `asr` příponu a je založen na zdrojových šifrovacích klíčích virtuálního počítače. Pokud trezor klíčů vytvořený obnovením webu již existuje, je znovu použit.
1. Vyberte **Přizpůsobit,** chcete-li vybrat vlastní trezory klíčů.

> [!NOTE]
> Azure Virtuální počítače s operačními systémy Windows a [povolené pro šifrování pomocí aplikace Azure AD](https://aka.ms/ade-aad-app) jsou momentálně podporované azure site recovery.

### <a name="track-replication-status"></a>Sledování stavu replikace

Po povolení replikace můžete sledovat stav úlohy.

1. V **nastavení**vyberte **Aktualizovat,** abyste získali nejnovější stav.
1. Sledujte průběh a stav takto:
   1. Sledujte průběh úlohy **Povolit ochranu** v části**Úlohy** > **obnovení webu** **nastavení** > .
   1. V **nastavení** > **replikované položky**můžete zobrazit stav virtuálních počítače a počáteční průběh replikace. Vyberte virtuální hosti, chcete-li přejít k podrobnostem jeho nastavení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pro virtuální počítač Azure nakonfigurovali zotavení po havárii. Nyní můžete spustit postupzotavení po havárii a zkontrolovat, zda převzetí služeb při selhání funguje podle očekávání.

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md)
