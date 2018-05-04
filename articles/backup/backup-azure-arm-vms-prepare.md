---
title: 'Zálohování Azure: Příprava k zálohování virtuálních počítačů | Microsoft Docs'
description: Ujistěte se, že vaše prostředí je připravený pro zálohování virtuálních počítačů v Azure.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: zálohování; zálohování;
ms.assetid: e87e8db2-b4d9-40e1-a481-1aa560c03395
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/1/2018
ms.author: markgal;trinadhk;sogup;
ms.openlocfilehash: ba74a95d64edb8e795b9a521308435d5af11176e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="prepare-your-environment-to-back-up-resource-manager-deployed-virtual-machines"></a>Příprava prostředí pro zálohování virtuálních počítačů s nasazením Resource Manageru

Tento článek obsahuje kroky pro přípravu prostředí pro zálohování Azure Resource Manager nasadit virtuální počítač (VM). Postupy v postupech pomocí portálu Azure. Při zálohování virtuálního počítače, body obnovení, nebo zálohovaná data jsou uložené v trezoru služeb zotavení. Trezory služeb zotavení ukládat zálohovaná data pro classic a Resource Manager nasazené virtuální počítače.

> [!NOTE]
> Azure má dva modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../azure-resource-manager/resource-manager-deployment-model.md).

Před chránit (nebo zálohování) virtuálních počítačů nasazených Resource Managerem zkontrolujte existují tyto požadavky:

* Vytvoření nebo určení trezoru služeb zotavení *ve stejné oblasti jako virtuální počítač*.
* Vyberte scénář, definování zásad zálohování a určení položek k ochraně.
* Kontrola instalace agenta virtuálního počítače (rozšíření) na virtuálním počítači.
* Zkontrolujte síťové připojení.
* Virtuální počítače s Linuxem, pokud chcete přizpůsobit zálohování prostředí pro zálohování konzistentní s aplikací, postupujte podle [postup konfigurace skripty snímek před a po pořízení snímku](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

Pokud tyto podmínky se již existují ve vašem prostředí, pokračujte [zálohování virtuálních počítačů](backup-azure-arm-vms.md) článku. Pokud potřebujete nastavit nebo zkontrolujte jakýkoliv z těchto předpokladů, tento článek vás provede kroky.

## <a name="supported-operating-systems-for-backup"></a>Podporované operační systémy pro zálohování
 * **Linux**: Azure Backup podporuje [seznam distribuce, které Azure nezodpovídá](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), s výjimkou jádro operačního systému Linux. 
 
    > [!NOTE] 
    > Další přineste vaše – vlastní Linuxových distribucích může fungovat, dokud agent virtuálního počítače je k dispozici na virtuálním počítači a podpora pro Python existuje. Tyto distribuce ale podporované nejsou.
 * **Windows Server**: Verze starší než Windows Server 2008 R2 nejsou podporovány.

## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Omezení při zálohování a obnovení virtuálních počítačů
Než se připravíte prostředí, ujistěte se, že jste pochopili tato omezení:

* Zálohování virtuálních počítačů s více než 16 datových disků není podporována.
* Zálohování virtuálních počítačů s vyhrazenou IP adresu a žádný definovaný koncový bod není podporována.
* Zálohování virtuálních počítačů Linux zašifrovaná pomocí šifrování Linux Unified klíč instalační program (LUKS) není podporována.
* Není doporučeno, zálohování virtuálních počítačů, které obsahují sdílené svazky clusteru (CSV) nebo souborový Server škálovaný na konfiguraci. Když dokončíte, očekává se selhání zapisovačů sdíleného svazku clusteru. Vyžadují zahrnující všechny virtuální počítače, které jsou součástí konfigurace clusteru během úlohu snímku. Zálohování Azure nepodporuje konzistence více virtuálních počítačů. 
* Zálohovaná data neobsahuje sítě připojené jednotky připojené k virtuálnímu počítači.
* Nahrazení existujícího virtuálního počítače během obnovení se nepodporuje. Pokud se pokusíte obnovit virtuální počítač, když virtuální počítač existuje, operaci obnovení se nezdaří.
* Mezi oblastmi zálohování a obnovení nejsou podporovány.
* Při konfiguraci back up, zajistěte, aby **brány firewall a virtuální sítě** nastavení účtu úložiště povolit přístup ze všech sítí.
* Pro vybrané sítě, po dokončení konfigurace nastavení virtuální sítě a brány firewall pro váš účet úložiště, vyberte **Povolit důvěryhodné služby společnosti Microsoft pro přístup k tomuto účtu úložiště** jako výjimku do povolení služby Azure Backup přístup k účtu úložiště sítě omezený.
* Můžete zálohovat virtuální počítače ve všech veřejných oblastech Azure. (Viz [kontrolní seznam](https://azure.microsoft.com/regions/#services) z podporovaných oblastí.) Pokud oblast, kterou hledáte, není podporován dnes, nezobrazí se v rozevíracím seznamu při vytváření trezoru.
* Obnovení řadiče domény (DC) virtuálního počítače, který je součástí konfigurace více – řadič domény je možné pouze pomocí prostředí PowerShell. Další informace najdete v tématu [obnovení řadiče domény, řadiče domény služby více](backup-azure-arm-restore-vms.md#restore-domain-controller-vms).
* Obnovení virtuálních počítačů, které mají následující zvláštní síťové konfigurace je podporována pouze pomocí prostředí PowerShell. Po dokončení operace obnovení, nebude mít virtuální počítače vytvořené pomocí obnovení pracovního postupu v uživatelském rozhraní tyto konfigurace sítě. Další informace najdete v tématu [obnovení virtuálních počítačů s konfigurací speciální síťových](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations).
  * Virtuální počítače v části Konfigurace služby Vyrovnávání zatížení (interních a externích)
  * Virtuální počítače s více vyhrazené IP adresy
  * Virtuální počítače s více síťových adaptérů

## <a name="create-a-recovery-services-vault-for-a-vm"></a>Vytvoření trezoru služeb zotavení pro virtuální počítač
Trezor služeb zotavení je entita, která ukládá zálohy a body obnovení, které byly vytvořeny v čase. Trezor služeb zotavení obsahuje také zásady zálohování, které jsou přidružené chráněných virtuálních počítačů.

Chcete-li vytvořit trezor Služeb zotavení:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Na **rozbočovače** nabídce vyberte možnost **Procházet**a pak zadejte **služeb zotavení**. Během zadávání váš vstup filtrování seznamu prostředků. Vyberte **trezory služeb zotavení**.

    ![Zadáním v poli a výběr ve výsledcích "Trezory služeb zotavení"](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

    Objeví se seznam trezorů Služeb zotavení.
3. Na **trezory služeb zotavení** nabídce vyberte možnost **přidat**.

    ![Vytvoření trezoru Recovery Services – krok 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

    **Trezory služeb zotavení** otevře se podokno. Budete vyzváni k zadání informací o pro **název**, **předplatné**, **skupiny prostředků**, a **umístění**.

    ![Podokno "Trezory služeb zotavení"](./media/backup-azure-arm-vms-prepare/rs-vault-attributes.png)
4. Jako **Název** zadejte popisný název pro identifikaci trezoru. Název musí být jedinečný v rámci předplatného Azure. Zadejte název, který obsahuje 2 až 50 znaků. Musí začínat písmenem a může obsahovat pouze písmena, číslice a pomlčky.
5. Vyberte **předplatné** zobrazíte seznam dostupných předplatných. Pokud si nejste jisti, jaké předplatné použít, použijte výchozí (nebo navrhované) předplatné. Existuje několik možností, pouze pokud vaše pracovní nebo školní účet je přidružen více předplatných Azure.
6. Vyberte **skupiny prostředků** najdete v seznamu dostupných skupin prostředků, nebo vyberte **nový** vytvořit novou skupinu prostředků. Úplné informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).
7. Vyberte **umístění** vyberte zeměpisnou oblast trezoru. Trezor *musí* být ve stejné oblasti jako virtuální počítače, které chcete chránit.

   > [!IMPORTANT]
   > Pokud si nejste jisti oblastí, ve kterém je váš virtuální počítač, zavřete dialogové okno Vytvoření trezoru a přejít na seznam virtuálních počítačů na portálu. Pokud máte virtuální počítače v několika oblastech, budete muset vytvořit trezor služeb zotavení v každé oblasti. Vytvořte trezor nejprve v první oblasti, poté přejděte k další oblasti. Není potřeba specifikovat účty úložiště pro ukládání dat záloh. Trezor služeb zotavení a služba Azure Backup zpracování, automaticky.
   >
   >

8. Vyberte **Vytvořit**. Vytvoření trezoru Služeb zotavení může chvíli trvat. Sledujte oznámení o stavu v oblasti pravé horní části portálu. Po vytvoření svůj trezor se zobrazí v seznamu trezorů služeb zotavení. Pokud svůj trezor nevidíte, vyberte **aktualizovat**.

    ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

Teď, když jste vytvořili trezor, se naučte, jak nastavit replikaci úložiště.

## <a name="set-storage-replication"></a>Nastavit replikaci úložiště
Možnost replikace úložiště umožňuje výběr mezi geograficky redundantním úložištěm a místně redundantního úložiště. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště. Ponechejte možnost nastavení jako geograficky redundantní úložiště pro vaši primární zálohu. Pokud chcete levnější možnost, která není jako trvalé, zvolte místně redundantní úložiště.

Chcete-li upravit nastavení replikace úložiště:

1. Na **trezory služeb zotavení** podokně, vyberte svůj trezor.
    Když vyberete svůj trezor **nastavení** podokno (což je název trezoru nahoře) a v podokně podrobností trezor otevřete.

   ![Zvolte svůj trezor ze seznamu trezorů záloh](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)

2. Na **nastavení** podokně posuvníkem svislé přejděte dolů k položce **spravovat** a vyberte **infrastruktura zálohování**. V **Obecné** vyberte **konfigurace zálohování**. Na **konfigurace zálohování** podokně, vyberte možnost replikace úložiště pro svůj trezor. Ve výchozím nastavení má váš trezor nastavené geograficky redundantní úložiště.

   ![Seznam trezorů záloh](./media/backup-azure-arm-vms-prepare/full-blade.png)

   Pokud používáte Azure jako koncový bod primární úložiště záloh, pokračujte v používání geograficky redundantní úložiště. Pokud používáte Azure jako koncový bod úložiště záloh není primární, zvolte místně redundantní úložiště. Další informace o možnosti úložiště v [Přehled replikace Azure Storage](../storage/common/storage-redundancy.md).

3. Pokud jste změnili typu replikace úložiště, vyberte **Uložit**.
    
Po výběru možnosti úložiště pro svůj trezor, budete připraveni k přidružení virtuálního počítače s úložištěm. Chcete-li začít přidružení, měli byste vyhledat a zaregistrovat virtuální počítače Azure.

## <a name="select-a-backup-goal-set-policy-and-define-items-to-protect"></a>Výběr cíle zálohování, nastavení zásad a určení položek k ochraně
Předtím, než zaregistrujete virtuálního počítače k trezoru služeb zotavení, spusťte proces vyhledávání pro identifikaci všechny nové virtuální počítače přidat do odběru. Proces zjišťování dotazuje na seznam virtuálních počítačů v rámci předplatného Azure. Pokud se najde nové virtuální počítače, na portálu zobrazí název cloudové služby a přidružené oblasti. Na portálu Azure *scénář* je zadejte v trezoru služeb zotavení. *Zásady* je plán, jak často a kdy jsou pořizovány body obnovení. Zásada také obsahuje rozsah uchování bodů obnovení.

1. Pokud již máte otevřený trezor Služeb zotavení, pokračujte ke kroku 2. Pokud nemáte trezoru služeb zotavení otevřít, otevřete [portál Azure](https://portal.azure.com/). Na **rozbočovače** nabídce vyberte možnost **další služby**.

   a. V seznamu prostředků zadejte **Recovery Services**. Během zadávání váš vstup filtrování seznamu. Až se zobrazí **trezory služeb zotavení**, vyberte ho.

      ![Zadáním v poli a výběr ve výsledcích "Trezory služeb zotavení"](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

      Objeví se seznam trezorů Služeb zotavení. Pokud ve vašem předplatném nejsou žádné trezory, tento seznam je prázdný.

      ![Zobrazení seznamu trezorů služby Recovery Services](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

   b. Ze seznamu trezorů Služeb zotavení vyberte trezor.

      **Nastavení** trezoru otevřete podokno a řídícím panelu trezoru pro zvolený.

      ![Nastavení řídicího panelu podokno a trezoru](./media/backup-azure-arm-vms-prepare/new-vault-settings-blade.png)
2. V nabídce řídícího panelu trezoru, vyberte **zálohování**.

   ![Zálohování tlačítko](./media/backup-azure-arm-vms-prepare/backup-button.png)

   **Zálohování** a **cíl zálohování** otevřete podokna.

3. Na **cíle zálohování** podokně nastavit **kde běží vaše úloha?** jako **Azure** a **co chcete zálohovat?** jako  **Virtuální počítač**. Pak vyberte **OK**.

   ![Zálohování a cíl zálohování podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

   Tento krok zaregistruje rozšíření virtuálního počítače s úložištěm. **Cíl zálohování** podokně zavře a **zálohování zásad** otevře se podokno.

   !["Zálohování" a "Zálohování zásady" podokna](./media/backup-azure-arm-vms-prepare/select-backup-goal-2.png)
4. Na **zálohování zásad** podokně vyberte zásadu zálohování, který chcete použít pro trezor.

   ![Výběr zásady zálohování](./media/backup-azure-arm-vms-prepare/setting-rs-backup-policy-new.png)

   Podrobnosti výchozí zásady jsou uvedené pod rozevírací nabídkou. Chcete-li vytvořit novou zásadu, vyberte z rozevírací nabídky **Vytvořit novou**. Pokyny k definování zásad zálohování naleznete v tématu [Definování zásad zálohování](backup-azure-vms-first-look-arm.md#defining-a-backup-policy).
    Vyberte **OK** přidružení zásady zálohování k trezoru.

   **Zálohování zásad** podokně zavře a **vybrat virtuální počítače** otevře se podokno.
5. Na **vybrat virtuální počítače** podokně zvolit virtuální počítače, které chcete přidružit k určené zásadě a vyberte **OK**.

   !["Vyberte virtuální počítače" podokno](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

   Vybraný virtuální počítač se ověří. Pokud nevidíte očekávaných virtuálních počítačů, zkontrolujte, že virtuální počítače jsou ve stejné oblasti Azure jako trezor služeb zotavení. Pokud virtuální počítače stále nezobrazí, zkontrolujte, nejsou již chráněny pomocí jiné trezoru. Řídícím panelu trezoru ukazuje oblasti, kde existuje trezor služeb zotavení.

6. Teď, když jste definovali všechna nastavení trezoru, na **zálohování** podokně, vyberte **povolit zálohování**. Tento krok nasadí zásadu pro trezor a virtuální počítače. Tento krok nevytváří vytvoření počátečního bodu obnovení pro virtuální počítač.

   ![Tlačítko "Povolit zálohování"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Jakmile povolíte úspěšně zálohování, zásady zálohování se spustí podle plánu. Pokud chcete generovat úlohu zálohování na vyžádání pro zálohování virtuálních počítačů, vidět [aktivuje úloha zálohování](./backup-azure-vms-first-look-arm.md#initial-backup).

Pokud máte potíže s registrací virtuální počítač, zobrazíte následující informace o instalaci agenta virtuálního počítače a na připojení k síti. Pravděpodobně ani nepotřebujete následující informace Pokud chráníte virtuální počítače vytvořené v Azure. Ale pokud jste migrovali virtuální počítače Azure, ujistěte se, zda správně nainstalovaný agent virtuálního počítače a virtuální počítač může komunikovat se službou virtuální sítě.

## <a name="install-the-vm-agent-on-the-virtual-machine"></a>Nainstalujte agenta virtuálního počítače na virtuálním počítači
Pro rozšíření Backup pracovat, Azure [agenta virtuálního počítače](../virtual-machines/windows/agent-user-guide.md) musí být nainstalován na virtuální počítač Azure. Pokud byl váš virtuální počítač vytvořen z Azure Marketplace, agent virtuálního počítače již existuje ve virtuálním počítači. 

Následující informace slouží pro situacích, kdy jsou *není* pomocí virtuální počítač vytvořen z Azure Marketplace. Například jste migrovali virtuální počítač z překážek místní datacentra. V takovém případě musí být nainstalovaný za účelem ochrany virtuálního počítače agenta virtuálního počítače.

Pokud máte problémy se zálohováním virtuálního počítače Azure, zkontrolujte, zda je na virtuálním počítači správně nainstalován agent virtuálního počítače Azure pomocí následující tabulky. Tabulka obsahuje další informace o agenta virtuálního počítače pro systém Windows a virtuální počítače s Linuxem.

| **Operace** | **Windows** | **Linux** |
| --- | --- | --- |
| Nainstalujte agenta virtuálního počítače |Stáhněte si a nainstalujte [MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Budete potřebovat oprávnění správce k dokončení instalace. |Nainstalujte si nejnovější verzi [agenta systému Linux](../virtual-machines/linux/agent-user-guide.md). Budete potřebovat oprávnění správce k dokončení instalace. Doporučujeme nainstalovat agenta z distribuční úložiště. Jsme *nedoporučujeme* instalace agenta virtuálního počítače s Linuxem přímo z Githubu.  |
| Aktualizace agenta virtuálního počítače |Aktualizace agenta virtuálního počítače je stejně jednoduché jako přeinstalace [binárních souborů agenta virtuálního počítače](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br><br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |Postupujte podle pokynů pro [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Doporučujeme aktualizovat agenta z distribuční úložiště. Jsme *nedoporučujeme* aktualizace agenta virtuálního počítače s Linuxem přímo z Githubu.<br><br>Ujistěte se, že během aktualizace agenta virtuálního počítače neběží žádná operace zálohování. |
| Ověření instalace agenta virtuálního počítače |1. Přejděte do složky C:\WindowsAzure\Packages ve virtuálním počítači Azure. <br><br>2. Najít soubor WaAppAgent.exe. <br><br>3. Pravým tlačítkem myši klikněte na soubor, přejděte na **Vlastnosti** a poté vyberte kartu **Podrobnosti**. **Verze produktu** pole by mělo být 2.6.1198.718 nebo vyšší. |neuvedeno |

### <a name="backup-extension"></a>Rozšíření zálohování
Po instalaci agenta virtuálního počítače na virtuálním počítači nainstaluje služba Azure Backup agent virtuálního počítače rozšíření zálohování. Služba Backup bezproblémově upgraduje a opravuje rozšíření zálohování.

Služba zálohování nainstaluje rozšíření zálohování, zda je virtuální počítač spuštěný. Spuštěný virtuální počítač poskytuje největší šanci získání bodu obnovení, který je konzistentní v rámci aplikace. Služba zálohování se však nadále zálohovat virtuální počítač, i když je vypnutý a rozšíření nebylo možné nainstalovat. To se označuje jako *offline virtuálního počítače*. V takovém případě bude bod obnovení *konzistentní pro případ chyby*.

## <a name="establish-network-connectivity"></a>Vytvoření připojení k síti
Pokud chcete spravovat snímky virtuálních počítačů, potřebuje rozšíření zálohování připojení k Azure veřejné IP adresy. Bez správné připojení k Internetu vypršení časového limitu pro požadavky HTTP virtuálního počítače a operaci zálohování se nezdaří. Pokud má vaše nasazení omezení přístupu na místě – prostřednictvím skupinu zabezpečení sítě (NSG), například – vyberte jednu z těchto možností k zadání zrušte cesty pro provoz zálohování:

* [Seznam povolených adres Azure datacenter IP rozsahy](http://www.microsoft.com/en-us/download/details.aspx?id=41653).
* Nasazení proxy server HTTP pro směrování provozu.

Pokud máte možnost provedení při rozhodování o tom, jsou kompromisy mezi možnosti správy, přesná kontrola a náklady.

| Možnost | Výhody | Nevýhody |
| --- | --- | --- |
| Rozsahy povolených IP adres |Žádné dodatečné poplatky.<br><br>Pro přístup k otevření v skupinu NSG, použijte **Set-AzureNetworkSecurityRule** rutiny. |Komplexní správa jako ovlivněný časem změnit rozsahy IP adres.<br><br>Poskytuje přístup k celé Azure a ne jenom úložiště. |
| Použít proxy server HTTP |Přesná kontrola v proxy přes úložiště adresy URL je povolen.<br><br>Jeden bod internet přístup k virtuálním počítačům.<br><br>Není v souladu změny Azure IP adresy. |Další náklady pro spuštění virtuálního počítače s proxy software. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Seznam povolených adres Azure datacenter rozsahy IP adres
Seznam povolených adres rozsahy IP datové centrum Azure, najdete v článku [webu Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) podrobnosti o rozsahy IP adres a pokyny.

Připojení k úložišti konkrétní oblasti můžete povolit pomocí [služby značky](../virtual-network/security-overview.md#service-tags). Ujistěte se, že pravidlo, které umožňuje přístup k účtu úložiště má vyšší prioritu než pravidlo, které blokuje přístup k Internetu. 

![Skupina NSG s značky úložiště pro oblast](./media/backup-azure-arm-vms-prepare/storage-tags-with-nsg.png)

Následující video vás provede krok za krokem postup konfigurace služby značky: 

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

> [!WARNING]
> Značky služby úložiště jsou k dispozici pouze v určitých oblastí a jsou ve verzi preview. Seznam oblastí naleznete v tématu [služby značky pro úložiště](../virtual-network/security-overview.md#service-tags).

### <a name="use-an-http-proxy-for-vm-backups"></a>Použít proxy server HTTP pro zálohování virtuálních počítačů
Pokud zálohujete virtuální počítač, rozšíření zálohování na virtuální počítač odešle snímek příkazy pro správu do služby Azure Storage pomocí rozhraní API HTTPS. Směrujte provoz rozšíření zálohování přes server proxy protokolu HTTP, protože se jedná o jedinou komponentou nakonfigurovaný pro přístup do veřejného Internetu.

> [!NOTE]
> Nedoporučujeme konkrétní proxy software, který byste měli používat. Ujistěte se, že vyberete proxy server, který je kompatibilní s kroky konfigurace následujících.
>
>

Následující obrázek příklad ukazuje tři konfiguračních kroků nezbytných k používání proxy serveru HTTP:

* Trasy virtuálního počítače aplikace veškerý provoz protokolu HTTP vázaný veřejného Internetu prostřednictvím proxy serveru virtuálního počítače.
* Proxy virtuálních počítačů umožňuje příchozí provoz z virtuálních počítačů ve virtuální síti.
* Skupina zabezpečení sítě s názvem NFP uzamčení musí pravidlo zabezpečení, který umožňuje odchozí internetové přenosy z virtuálního počítače proxy serveru.

Ke komunikaci s veřejného Internetu používat proxy server HTTP, proveďte následující kroky.

> [!NOTE]
> Tyto kroky používají konkrétní názvy a hodnoty v tomto příkladu. Se při přechodu do (nebo vložením) podrobností do kódu, použijte názvy a hodnoty pro vaše nasazení.

#### <a name="step-1-configure-outgoing-network-connections"></a>Krok 1: Konfigurace odchozích síťových připojení
###### <a name="for-windows-machines"></a>Pro počítače s Windows
Tento postup nastaví konfiguraci proxy serveru pro místní systémový účet.

1. Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Otevřete Internet Explorer tak, že z řádku se zvýšenými oprávněními spustíte následující příkaz:

    ```
    psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
    ```

3. V Internet Exploreru přejděte na **nástroje** > **Možnosti Internetu** > **připojení** > **nastavení místní sítě**.
4. Ověřte nastavení proxy pro účet system. Nastavení proxy IP a portu.
5. Zavřete Internet Explorer.

Následující skript nastaví konfiguraci proxy celého systému a používá je pro všechny odchozí přenosy protokolu HTTP nebo HTTPS. Pokud jste nastavili proxy server na aktuální uživatelský účet (nikoli účet místního systému), použijte tento skript je aplikovat na SYSTEMACCOUNT.

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Pokud zjistíte v protokolu serveru proxy "(407) vyžadováno ověření proxy serveru", zkontrolujte správně nastavit ověřování.
>
>

###### <a name="for-linux-machines"></a>Pro počítače se systémem Linux
Přidejte následující řádek na ```/etc/environment``` souboru:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Přidejte následující řádky, které se ```/etc/waagent.conf``` souboru:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Krok 2: Povolte příchozí připojení na serveru proxy
1. Na serveru proxy otevřete bránu Windows Firewall. Nejjednodušší způsob, jak získat přístup k bráně firewall je pro vyhledávání **brány Windows Firewall s pokročilým zabezpečením**.
2. V **brány Windows Firewall s pokročilým zabezpečením** dialogové okno, klikněte pravým tlačítkem na **příchozí pravidla** a vyberte **nové pravidlo**.
3. V nástroji příchozí pravidlo Průvodce novou na **typ pravidla** vyberte **vlastní** možnost a vyberte **Další**.
4. Na **programu** vyberte **všechny programy** a vyberte **Další**.
5. Na **protokol a porty** stránky, zadejte následující informace a vyberte **Další**:
   * Pro **protokolu typ**, vyberte **TCP**.
   * Pro **místního portu**, vyberte **specifické porty**. Do následujícího pole zadejte číslo portu proxy serveru, který byl nakonfigurován.
   * Pro **vzdálených portů**, vyberte **všechny porty**.

Pro zbytek průvodce přijměte výchozí nastavení, až na konci. Pak pojmenujte toto pravidlo. 

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Krok 3: Přidejte pravidlo výjimky pro NSG
Následující příkaz přidá výjimku NSG. Tato výjimka umožňuje TCP provoz z jakéhokoli portu na 10.0.0.5 všechny internetové adresy na portu 80 (HTTP) nebo 443 (HTTPS). Pokud budete potřebovat konkrétní port na veřejný internet, je nutné přidat tento port k ```-DestinationPortRange```.

V příkazovém řádku prostředí Azure PowerShell zadejte následující příkaz:

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

## <a name="questions"></a>Máte dotazy?
Pokud máte dotazy nebo pokud je všechny funkce, kterou chcete uvítali, [pošlete nám svůj názor](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Další postup
Teď, když připravíte prostředí pro zálohování virtuálního počítače, vaše další logické krokem je vytvoření zálohy. Plánování článek poskytuje podrobnější informace o zálohování virtuálních počítačů.

* [Zálohování virtuálních počítačů](backup-azure-arm-vms.md)
* [Plánování vaší infrastruktury zálohování virtuálních počítačů](backup-azure-vms-introduction.md)
* [Správa záloh virtuálních počítačů](backup-azure-manage-vms.md)
