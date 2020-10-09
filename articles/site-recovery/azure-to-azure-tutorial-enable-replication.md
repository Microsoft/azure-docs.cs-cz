---
title: Nastavení zotavení po havárii virtuálního počítače Azure pomocí Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii pro virtuální počítače Azure do jiné oblasti Azure pomocí služby Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421412"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Nastavení zotavení po havárii pro virtuální počítače Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá k strategii zotavení po havárii tím, že spravuje a orchestruje replikaci, převzetí služeb při selhání a navrácení služeb po obnovení místních počítačů a virtuálních počítačů Azure (VM).

V tomto kurzu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítače Azure jejich replikací z jedné oblasti Azure do jiné. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření trezoru Služeb zotavení
> * Ověření nastavení cílových prostředků
> * Nastavení odchozího připojení k síti pro virtuální počítače
> * Povolení replikace virtuálního počítače

> [!NOTE]
> Tento článek poskytuje pokyny pro nasazení zotavení po havárii s nejjednodušším nastavením. Pokud se chcete dozvědět o přizpůsobených nastaveních, přečtěte si články v [části postupy](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Prostudujte si [architekturu a komponenty scénáře](./azure-to-azure-architecture.md).
- Než začnete, přečtěte si [požadavky na podporu](./azure-to-azure-support-matrix.md) .

## <a name="create-a-recovery-services-vault"></a>Vytvoření trezoru Služeb zotavení

Vytvořte trezor v libovolné oblasti, s výjimkou zdrojové oblasti.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**. Pak vyberte **IT & nástroje pro správu**  >  **zálohování a Site Recovery**.
1. Do pole **název**zadejte popisný název pro identifikaci trezoru. Pokud máte více předplatných, vyberte příslušné předplatné.
1. Vytvořte skupinu prostředků nebo vyberte existující. Zadejte oblast Azure. Pokud chcete zjistit podporované oblasti, přečtěte si článek geografická dostupnost v [Azure Site Recovery podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Pokud chcete získat přístup k trezoru z řídicího panelu, vyberte **Připnout na řídicí panel** a pak vyberte **vytvořit**.

   ![Nový trezor](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

Nový trezor se přidá do oblasti **Řídicí panel** v části **Všechny prostředky** a na hlavní stránku **Trezory Recovery Services**.

## <a name="verify-target-resource-settings"></a>Ověření nastavení cílových prostředků

Ověřte předplatné Azure pro cílovou oblast.

- Ověřte, že vaše předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti. O povolení požadované kvóty požádejte podporu.
- Ujistěte se, že vaše předplatné má dostatek prostředků pro podporu velikostí virtuálních počítačů, které se shodují se zdrojovými virtuálními počítači. Site Recovery pro cílový virtuální počítač vybere stejnou velikost nebo nejbližší možnou velikost.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Nastavení odchozího připojení k síti pro virtuální počítače

Aby Site Recovery fungoval podle očekávání, musíte změnit odchozí připojení k síti z virtuálních počítačů, které chcete replikovat.

> [!NOTE]
> Site Recovery nepodporuje připojení k síti pomocí ověřovacího proxy serveru.

### <a name="outbound-connectivity-for-urls"></a>Odchozí připojení pro adresy URL

Pokud k řízení odchozího připojení používáte proxy server brány firewall založený na adrese URL, povolte přístup k těmto adresám URL:

| **Název**                  | **Komerční**                               | **Státní správa**                                 | **Popis** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Umožňuje zápis dat z virtuálního počítače do účtu úložiště mezipaměti ve zdrojové oblasti. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Zajišťuje autorizaci a ověřování pro adresy URL služby Site Recovery. |
| Replikace               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Umožňuje komunikaci virtuálního počítače se službou Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Umožňuje virtuálnímu počítači zapisovat data monitorování a diagnostiky Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Odchozí připojení pro rozsahy IP adres

Pokud používáte skupinu zabezpečení sítě (NSG), vytvořte pravidla NSG založená na značkách služby pro přístup k Azure Storage, Azure Active Directory, Site Recovery službě a sledování Site Recovery. [Další informace](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Ověření certifikátů virtuálních počítačů Azure

Ověřte, že virtuální počítače, které chcete replikovat, mají nejnovější kořenové certifikáty. Pokud ne, virtuální počítač nejde zaregistrovat do Site Recovery z důvodu omezení zabezpečení.

- U virtuálních počítačů s Windows zajistíte přítomnost všech důvěryhodných kořenových certifikátů tím, že na ně nainstalujete všechny nejnovější aktualizace Windows. V odpojeném prostředí dodržujte při aktualizaci Windows a certifikátů standardní postupy uplatňované ve vaší organizaci.
- U virtuálních počítačů s Linuxem postupujte při získávání nejnovějších důvěryhodných kořenových certifikátů a seznamu odvolaných certifikátů na virtuálním počítači podle pokynů, které jste dostali od distributora Linuxu.

## <a name="set-permissions-on-the-account"></a>Nastavení oprávnění k účtu

Azure Site Recovery poskytuje pro řízení operací správy Site Recovery tři předdefinované role.

- **Přispěvatel Site Recovery** – Tato role má všechna oprávnění potřebná ke správě operací Azure Site Recovery v trezoru služby Recovery Services. Uživatel s touto rolí však nemůže vytvořit ani odstranit trezor služby Recovery Services ani přiřadit přístup jiným uživatelům. Tato role je nejvhodnější pro správce zotavení po havárii, kteří můžou povolit a spravovat zotavení po havárii pro aplikace nebo celé organizace.

- **Operátor Site Recovery** – Tato role má oprávnění provádět a spravovat operace převzetí služeb při selhání a navrácení služeb po obnovení. Uživatel s touto rolí nemůže povolit ani zakázat replikaci, vytvářet ani odstraňovat trezory, registrovat novou infrastrukturu ani přiřadit přístupová práva jiným uživatelům. Tato role je nejvhodnější pro operátory zotavení po havárii, kteří můžou na pokyn vlastníků aplikací nebo správců IT provést převzetí služeb při selhání virtuálních počítačů nebo aplikací. Po vyřešení havárie může operátor zotavení po havárii znovu nastavit ochranu a navrátit služby po obnovení virtuálních počítačů.

- **Čtenář Site Recovery** – Tato role má oprávnění zobrazit všechny operace správy Site Recovery. Tato role je nejvhodnější pro vedoucí monitorování IT, kteří můžou monitorovat aktuální stav ochrany a vydávat lístky podpory.

Přečtěte si další informace o [předdefinovaných rolích Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Povolení replikace virtuálního počítače

Následující části popisují, jak povolit replikaci.

### <a name="select-the-source"></a>Výběr zdroje

Pokud chcete spustit nastavení replikace, vyberte zdroj, ve kterém běží vaše virtuální počítače Azure.

1. Přejít na **Recovery Services trezory**, vyberte název trezoru a pak vyberte **+ replikovat**.
1. Jako **zdroj**vyberte **Azure**.
1. V rozevíracím seznamu **Umístění zdroje** vyberte zdrojovou oblast Azure, kde máte virtuální počítače aktuálně spuštěné.
1. Vyberte **Zdrojové předplatné**, ve kterém jsou virtuální počítače spuštěné. Může to být jakékoli předplatné ve stejném tenantovi Azure Active Directory, ve kterém se nachází váš trezor služby Recovery Services.
1. Vyberte **zdrojovou skupinu prostředků**a kliknutím na **OK** nastavení uložte.

   ![Nastavení zdroje](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Výběr virtuálních počítačů

Site Recovery načte seznam virtuálních počítačů přidružených k předplatnému a skupinu prostředků nebo cloudovou službu.

1. V části **Virtuální počítače** vyberte virtuální počítače, které chcete replikovat.
1. Vyberte **OK**.

### <a name="configure-replication-settings"></a>Konfigurace nastavení replikace

Site Recovery vytvoří výchozí nastavení a zásady replikace pro cílovou oblast. Tato nastavení můžete změnit podle potřeby.

1. Vyberte **Nastavení** pro zobrazení cíle a nastavení replikace.

1. Pokud chcete přepsat výchozí nastavení cíle, vyberte **přizpůsobit** u možnosti **Skupina prostředků, síť, úložiště a dostupnost**.

   ![Konfigurace nastavení](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Přizpůsobení nastavení cíle shrnuté v tabulce.

   | **Nastavení** | **Podrobnosti** |
   | --- | --- |
   | **Cílové předplatné** | Ve výchozím nastavení je cílové předplatné stejné jako zdrojové předplatné. Vyberte **přizpůsobit** a vyberte jiné cílové předplatné v rámci stejného Azure Active Directory tenanta. |
   | **Cílové umístění** | Cílová oblast použitá pro zotavení po havárii.<br/><br/> Doporučujeme, aby cílové umístění odpovídalo umístění trezoru Site Recovery. |
   | **Cílová skupina prostředků** | Skupina prostředků v cílové oblasti, která obsahuje virtuální počítače Azure po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení Site Recovery vytvoří novou skupinu prostředků v cílové oblasti s `asr` příponou. Umístění cílové skupiny prostředků může být libovolná oblast s výjimkou oblasti, ve které jsou hostované vaše zdrojové virtuální počítače. |
   | **Cílová virtuální síť** | Síť v cílové oblasti, ve které jsou virtuální počítače umístěné po převzetí služeb při selhání.<br/><br/> Ve výchozím nastavení Site Recovery vytvoří novou virtuální síť (a podsítě) v cílové oblasti s `asr` příponou. |
   | **Účty úložiště mezipaměti** | Site Recovery používá účet úložiště ve zdrojové oblasti. Do tohoto účtu se odešlou změny zdrojových virtuálních počítačů před replikací o cílového umístění.<br/><br/> Pokud používáte účet úložiště mezipaměti s podporou brány firewall, ujistěte se, že povolíte možnost **Povolit důvěryhodné služby společnosti Microsoft**. [Další informace](../storage/common/storage-network-security.md#exceptions). Také se ujistěte, že máte povolený přístup k alespoň jedné podsíti zdrojové virtuální sítě. |
   | **Cílové účty úložiště (zdrojový virtuální počítač používá jiné než spravované disky)** | Ve výchozím nastavení Site Recovery vytvoří v cílové oblasti nový účet úložiště pro zrcadlení účtu úložiště zdrojového virtuálního počítače.<br/><br/> Pokud používáte účet úložiště mezipaměti s podporou brány firewall, povolte možnost **Povolit důvěryhodné služby společnosti Microsoft** . |
   | **Repliky spravované disky (Pokud zdrojový virtuální počítač používá spravované disky)** | Ve výchozím nastavení Site Recovery vytvoří repliku spravované disky v cílové oblasti pro zrcadlení spravovaných disků zdrojového virtuálního počítače se stejným typem úložiště (Standard nebo Premium) jako spravovaný disk zdrojového virtuálního počítače. Můžete přizpůsobit jenom typ disku. |
   | **Cílové skupiny dostupnosti** | Ve výchozím nastavení Azure Site Recovery vytvoří novou skupinu dostupnosti v cílové oblasti s názvem s `asr` příponou pro virtuální počítače v rámci skupiny dostupnosti ve zdrojové oblasti. V případě, že sada dostupnosti, kterou vytvořil Azure Site Recovery, už existuje, použije se znovu. |
   | **Cílové zóny dostupnosti** | Ve výchozím nastavení Site Recovery přiřazuje stejné číslo zóny jako zdrojová oblast v cílové oblasti, pokud cílová oblast podporuje zóny dostupnosti.<br/><br/> Pokud cílová oblast nepodporuje zóny dostupnosti, cílové virtuální počítače se ve výchozím nastavení nakonfigurují jako samostatné instance.<br/><br/> Pokud chcete virtuální počítače nakonfigurovat jako součást skupiny dostupnosti v cílové oblasti, vyberte **přizpůsobit** .<br/><br/> Po povolení replikace nemůžete změnit typ dostupnosti (jediná instance, Skupina dostupnosti nebo zóna dostupnosti). Chcete-li změnit typ dostupnosti, zakažte a Povolte replikaci. |

1. Pokud chcete přizpůsobit nastavení zásad replikace, vyberte **přizpůsobit** u možnosti **zásada replikace**a upravte nastavení podle potřeby.

   | **Nastavení** | **Podrobnosti** |
   | --- | --- |
   | **Název zásad replikace** | Název zásady |
   | **Uchování bodu obnovení** | Ve výchozím nastavení Site Recovery udržuje body obnovení po dobu 24 hodin. Můžete nakonfigurovat hodnotu v rozmezí 1 až 72 hodin. |
   | **Frekvence snímků konzistentní vzhledem k aplikacím** | Ve výchozím nastavení Site Recovery přebírá snímek konzistentní vzhledem k aplikacím každé 4 hodiny. Můžete nakonfigurovat jakoukoli hodnotu v rozmezí 1 až 12 hodin.<br/><br/> Snímek konzistentní vzhledem k aplikacím je snímkem dat aplikací v rámci virtuálního počítače v čase. Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači budou při pořízení snímku v konzistentním stavu. |
   | **Replikační skupina** | Pokud vaše aplikace potřebuje konzistenci pro víc virtuálních počítačů napříč virtuálními počítači, můžete pro tyto virtuální počítače vytvořit replikační skupinu. Ve výchozím nastavení nejsou vybrané virtuální počítače součástí žádné replikační skupiny. |

1. Pokud chcete přidat virtuální počítače do nové nebo existující replikační skupiny, v **nastavení přizpůsobit**vyberte **Ano** pro konzistenci s více virtuálními počítači. Pak vyberte **OK**.

   > [!NOTE]
   > - Všechny počítače v replikační skupině mají při převzetí služeb při selhání sdílenou konzistentní chybu a body obnovení konzistentní vzhledem k aplikacím.
   > - Povolení konzistence s více virtuálními počítači může mít vliv na výkon úloh (což je náročné na procesor). Měla by se používat jenom v případě, že počítače používají stejnou úlohu a potřebujete konzistenci napříč několika počítači.
   > - V replikační skupině můžete mít maximálně 16 virtuálních počítačů.
   > - Pokud povolíte konzistenci napříč několika virtuálními počítači, budou spolu počítače v replikační skupině komunikovat přes port 20004. Ujistěte se, že brána firewall neblokuje interní komunikaci mezi virtuálními počítači přes tento port.
   > - V případě virtuálních počítačů se systémem Linux v replikační skupině ověřte, zda je odchozí provoz na portu 20004 otevřen ručně v souladu s pokyny pro verzi systému Linux.

### <a name="configure-encryption-settings"></a>Konfigurace nastavení šifrování

Pokud má zdrojový virtuální počítač povolenou službu Azure Disk Encryption (ADE), zkontrolujte nastavení.

1. Ověřte nastavení:
   1. **Trezory klíčů šifrování disku**: ve výchozím nastavení Site Recovery vytvoří nový trezor klíčů na klíčích pro šifrování disků zdrojového virtuálního počítače s `asr` příponou. Pokud Trezor klíčů již existuje, bude znovu použit.
   1. **Trezory klíčů šifrovacího**klíče: ve výchozím nastavení Site Recovery vytvoří nový trezor klíčů v cílové oblasti. Název má `asr` příponu a je založen na šifrovacích klíčích klíče zdrojového virtuálního počítače. Pokud Trezor klíčů vytvořený pomocí Site Recovery již existuje, bude znovu použit.
1. Vyberte **přizpůsobit** a vyberte vlastní trezory klíčů.

>[!NOTE]
> Site Recovery v současné době podporuje ADE s a bez Azure Active Directory (AAD) pro virtuální počítače s operačními systémy Windows. Pro operační systémy Linux podporujeme pouze ADE bez AAD. Pro počítače s ADE 1,1 (bez AAD) Navíc musí virtuální počítače používat spravované disky. Virtuální počítače s nespravovanými disky se nepodporují. Pokud přepnete z ADE 0,1 (s AAD) na 1,1, budete muset po povolení 1,1 zakázat replikaci a povolit replikaci pro virtuální počítač.

### <a name="track-replication-status"></a>Sledování stavu replikace

Po povolení replikace můžete sledovat stav úlohy.

1. V **Nastavení**vyberte **aktualizovat** , abyste získali nejnovější stav.
1. Sledujte průběh a stav následujícím způsobem:
   1. Sledovat průběh úlohy **Povolení ochrany** v **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**.
   1. V **Nastavení**  >  **replikované položky**můžete zobrazit stav virtuálních počítačů a průběh počáteční replikace. Vyberte virtuální počítač pro přechod k podrobnostem o jeho nastavení.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pro virtuální počítač Azure nakonfigurovali zotavení po havárii. Nyní můžete spustit postup zotavení po havárii a zkontrolovat, že převzetí služeb při selhání funguje podle očekávání.

> [!div class="nextstepaction"]
> [Spuštění postupu zotavení po havárii](azure-to-azure-tutorial-dr-drill.md)
