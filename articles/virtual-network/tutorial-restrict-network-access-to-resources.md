---
title: Omezení přístupu k prostředkům PaaS – kurz – Azure Portal
description: V tomto kurzu zjistíte, jak pomocí webu Azure Portal omezit síťový přístup k prostředkům Azure, jako jsou služby Azure Storage a Azure SQL Database, s využitím koncových bodů služeb.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/11/2020
ms.author: kumud
ms.openlocfilehash: cb3a4b6a726ee9163582b15586c65fc750712c63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97368241"
---
# <a name="tutorial-restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-portal"></a>Kurz: Omezení síťového přístupu k prostředkům PaaS s využitím koncových bodů služby pro virtuální síť pomocí webu Azure Portal

Koncové body služby pro virtuální síť umožňují omezení síťového přístupu k prostředkům některých služeb Azure na podsíť virtuální sítě. Můžete také odebrat internetový přístup k prostředkům. Koncové body služeb poskytují přímé připojení z vaší virtuální sítě k podporovaným službám Azure a umožňují pro přístup ke službám Azure použít privátní adresní prostor virtuální sítě. Provoz směřující do prostředků Azure prostřednictvím koncových bodů služby zůstává vždy v páteřní síti Microsoft Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření virtuální sítě s jednou podsítí
> * Přidání podsítě a povolení koncového bodu služby
> * Vytvoření prostředku Azure a povolení síťového přístupu k tomuto prostředku pouze z podsítě
> * Nasazení virtuálního počítače do každé podsítě
> * Ověření přístupu k prostředku z podsítě
> * Ověření odepření přístupu k prostředku z podsítě a internetu

Pokud chcete, můžete tento kurz absolvovat s použitím [Azure CLI](tutorial-restrict-network-access-to-resources-cli.md) nebo [Azure PowerShellu](tutorial-restrict-network-access-to-resources-powershell.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **sítě** a pak vyberte **virtuální sítě**.
3. Klikněte na **+ Přidat** a zadejte následující informace: 

   |Nastavení|Hodnota|
   |----|----|
   |Předplatné| Vyberte své předplatné.|
   |Skupina prostředků | Vyberte **Vytvořit novou** a zadejte *myResourceGroup*.|
   |Name| Zadejte *myVirtualNetwork* |
   |Oblast| Vyberte **(US) východní USA** |

   ![Zadání základních informací o virtuální síti](./media/tutorial-restrict-network-access-to-resources/create-virtual-network.png)

4. Klikněte na **Další: IP adresy >**
   
   |Nastavení|Hodnota|
   |----|----|
   |IPv4Address místo| Ponechat jako výchozí |
   |Název podsítě| Klikněte na **výchozí** a změňte název z "výchozí" na "public".|
   |Rozsah adres podsítě| Ponechat jako výchozí|

5. Klikněte na **Další: >zabezpečení** 
   
   |Nastavení|Hodnota|
   |----|----|   
   |BastionHost| Zakázat|
   |Ochrana před útoky DDoS| Zakázat|
   |Brána firewall| Zakázat|

6. Po dokončení klikněte na tlačítko **zkontrolovat a vytvořit**.
7. Pokud ověření projde, klikněte na **vytvořit**.
8. Počkejte, až se nasazení dokončí, a pak klikněte na **Přejít k prostředku** nebo přejděte k další části. 

## <a name="enable-a-service-endpoint"></a>Povolení koncového bodu služby

Koncové body služby se povolují pro každou službu a podsíť. Vytvoření podsítě a povolení koncového bodu služby pro podsíť:

1. Pokud na stránce prostředek virtuální sítě ještě nejste, můžete vyhledat nově vytvořenou síť v poli **Hledat prostředky, služby a dokumenty** v horní části portálu, zadat *myVirtualNetwork* a vybrat ho ze seznamu.
2. V nabídce **Nastavení** (vlevo) vyberte **podsítě** a pak vyberte **+ podsíť**, jak je znázorněno níže:

    ![Přidání podsítě](./media/tutorial-restrict-network-access-to-resources/add-subnet.png) 

3. V části **Přidat podsíť** vyberte nebo zadejte následující informace a pak vyberte **OK**:

    |Nastavení|Hodnota|
    |----|----|
    |Název| Privátní |
    |Rozsah adres| Ponechat jako výchozí|
    |Koncové body služby| Vyberte **Microsoft. Storage** .|
    |Zásady koncového bodu služby | Ponechte výchozí hodnotu 0. |

> [!CAUTION]
> Než povolíte koncový bod služby pro existující podsíť s prostředky, přečtěte si pokyny pro [změnu nastavení podsítě](virtual-network-manage-subnet.md#change-subnet-settings).

4. Klikněte na **Uložit** a pak na pravé straně zavřete okno podsíť. Nově vytvořená podsíť by měla být uvedena v seznamu.

## <a name="restrict-network-access-for-a-subnet"></a>Omezení síťového přístupu pro podsíť

Ve výchozím nastavení mohou všechny instance virtuálních počítačů v podsíti komunikovat se všemi prostředky. Můžete omezit komunikaci do a ze všech prostředků v podsíti tím, že vytvoříte skupinu zabezpečení sítě a přidružíte ji k podsíti:

1. V levém horním rohu webu Azure Portal vyberte **Všechny služby**.
2. Vyberte **sítě** a pak vyberte (nebo vyhledejte) **skupiny zabezpečení sítě**.
3. Na stránce **skupiny zabezpečení sítě** klikněte na **+ Přidat**.
4. Zadejte následující informace 

    |Nastavení|Hodnota|
    |----|----|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků | V seznamu vyberte *myResourceGroup*|
    |Name| Zadejte **myNsgPrivate** |
    |Umístění| Vyberte **východní USA** |

5. Klikněte na tlačítko **zkontrolovat + vytvořit** a po úspěšné kontrole ověřování klikněte na tlačítko **vytvořit**.
6. Po vytvoření skupiny zabezpečení sítě klikněte na **Přejít k prostředku** nebo vyhledejte *myNsgPrivate*.
7. V části **Nastavení** na levé straně vyberte **odchozí pravidla zabezpečení**.
8. Vyberte **+ Přidat**.
9. Vytvořte pravidlo pro povolení odchozí komunikace do služby Azure Storage. Zadejte nebo vyberte následující informace a pak vyberte **Přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Vyberte **VirtualNetwork**. |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **Značka služby**.|
    |Značka cílové služby | Vyberte **Storage**.|
    |Rozsahy cílových portů| Ponechte výchozí hodnotu *8080* . |
    |Protokol|Libovolný|
    |Akce|Povolit|
    |Priorita|100|
    |Název|Přejmenovat na **Allow-Storage-All**|

10. Vytvořte další odchozí pravidlo zabezpečení, které zakáže komunikaci s internetem. Toto pravidlo přepíše výchozí pravidlo ve všech skupinách zabezpečení sítě, které odchozí komunikaci s internetem povoluje. Proveďte kroky 6-9 z výše uvedených hodnot pomocí následujících hodnot:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Vyberte **VirtualNetwork**. |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **Značka služby**.|
    |Značka cílové služby| Vyberte **Internet**.|
    |Rozsahy cílových portů| * |
    |Protokol|Libovolný|
    |Akce|**Změnit výchozí na *Odepřít*** |
    |Priorita|110|
    |Name|Změnit na *Odepřít – Internet – vše*|

11. Vytvořte *příchozí pravidlo zabezpečení* , které umožní provoz protokol RDP (Remote Desktop Protocol) (RDP) do podsítě odkudkoli. Toto pravidlo přepíše výchozí pravidlo zabezpečení, které zakazuje veškerý příchozí provoz z internetu. Připojení ke vzdálené ploše jsou pro podsíť povolená z důvodu testování připojení v pozdějším kroku. 
12. V části **Nastavení** vyberte **příchozí pravidla zabezpečení**.
13. Vyberte **+ Přidat** a použijte následující hodnoty:

    |Nastavení|Hodnota|
    |----|----|
    |Zdroj| Libovolný |
    |Rozsahy zdrojových portů| * |
    |Cíl | Vyberte **VirtualNetwork**.|
    |Rozsahy cílových portů| Změňte na *3389* |
    |Protokol|Libovolný|
    |Akce|Povolit|
    |Priorita|120|
    |Name|Změnit na *Allow-RDP-All*|

   >[!WARNING] 
   > Port RDP 3389 je přístupný z Internetu. Tento postup se doporučuje jenom pro testování. V *produkčních prostředích* doporučujeme používat VPN nebo privátní připojení.

1.  V části **Nastavení** vyberte **Podsítě**.
2.  Klikněte na **+ Přidružit**.
3.  V části **virtuální síť** vyberte **myVirtualNetwork**.
4.  V části **podsíť** vyberte **soukromá** a pak vyberte **OK**.

## <a name="restrict-network-access-to-a-resource"></a>Omezení síťového přístupu k prostředku

Kroky potřebné k omezení síťového přístupu k prostředkům vytvořeným prostřednictvím služeb Azure, které jsou povolené pro koncové body služby, se budou lišit v rámci služeb. Konkrétní kroky pro jednotlivé služby najdete v dokumentaci příslušné služby. Zbývající část tohoto kurzu jako příklad obsahuje kroky k omezení síťového přístupu k účtu služby Azure Storage.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Do panelu vyhledávání zadejte "účet úložiště" a vyberte ho z rozevírací nabídky.
3. Klikněte na **+ Přidat**.
4. Zadejte následující informace:

    |Nastavení|Hodnota|
    |----|----|
    |Předplatné| Vyberte své předplatné.|
    |Skupina prostředků| Vybrat *myResourceGroup*|
    |Název účtu úložiště| Zadejte název, který je jedinečný ve všech umístěních Azure, je dlouhý 3 až 24 znaků a obsahuje pouze číslice a malá písmena.|
    |Umístění| Vyberte **(US) východní USA** |
    |Výkon|Standard|
    |Druh účtu| StorageV2 (obecné účely v2)|
    |Replikace| Místně redundantní úložiště (LRS)|

5. Vyberte **vytvořit + zkontrolovat** a po úspěšném ověření kontrol klikněte na **vytvořit**. 

>[!NOTE] 
> Dokončení nasazení může trvat několik minut.

6. Po vytvoření účtu úložiště klikněte na **Přejít k prostředku** .

### <a name="create-a-file-share-in-the-storage-account"></a>Vytvoření sdílené složky v účtu úložiště

1. Přejít na stránku Přehled účtu úložiště.
2. Vyberte ikonu **sdílet aplikace a pak klikněte na** **+ sdílení souborů**.

    |Nastavení|Hodnota|
    |----|----|
    |Název| Moje sdílení souborů|
    |Kvóta| ' Nastavit na maximum ' |

   ![Účet úložiště](./media/tutorial-restrict-network-access-to-resources/storage-account.png) 

3. Klikněte na **Vytvořit**.
4. Sdílená složka by se měla zobrazit v okně Azure, pokud ne klikněte na **aktualizovat** .

### <a name="restrict-network-access-to-a-subnet"></a>Omezení síťového přístupu k podsíti

Účty úložiště ve výchozím nastavení přijímají síťová připojení z klientů v jakékoli síti včetně internetu. Přístup k síti můžete omezit z Internetu a všech ostatních podsítí ve všech virtuálních sítích (s výjimkou *privátní* podsítě ve virtuální síti *myVirtualNetwork* ). Omezení síťového přístupu k podsíti:

1. V části **Nastavení** pro účet úložiště (s jedinečným názvem) vyberte **sítě**.
2. Vyberte **Vybrané sítě**.
3. Vyberte **+ Přidat existující virtuální síť**.
4. V části **Přidat sítě** vyberte následující hodnoty a pak vyberte **Přidat**:

    |Nastavení|Hodnota|
    |----|----|
    |Předplatné| Vyberte své předplatné.|
    |Virtuální sítě| **myVirtualNetwork**|
    |Podsítě| **Privátní**|

    ![Snímek obrazovky se zobrazí v podokně Přidat sítě, kde můžete zadat zadané hodnoty.](./media/tutorial-restrict-network-access-to-resources/virtual-networks.png)

5. Klikněte na **Přidat** a pak hned klikněte na ikonu **Uložit** . tím uložíte změny.
6. V části **Nastavení** účtu úložiště vyberte **přístupové klíče**, jak je znázorněno na následujícím obrázku:

      ![Snímek obrazovky se zobrazí přístupové klíče vybrané z nastavení, kde můžete získat klíč.](./media/tutorial-restrict-network-access-to-resources/storage-access-key.png)

7. Klikněte na **Zobrazit klíče** a poznamenejte si **klíčové** hodnoty, protože při mapování sdílené složky na písmeno jednotky ve virtuálním počítači budete muset ručně zadat klíč1 v pozdějším kroku.

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Pokud chcete otestovat síťový přístup k účtu úložiště, nasaďte do každé podsítě virtuální počítač.

### <a name="create-the-first-virtual-machine"></a>Vytvoření prvního virtuálního počítače

1. Z části Hledat prostředky. . ." na panelu vyhledejte **virtuální počítače**.
2. Vyberte **+ přidat > virtuální počítač**. 
3. Zadejte následující informace:

   |Nastavení|Hodnota|
   |----|----|
   |Předplatné| Vyberte své předplatné.|
   |Skupina prostředků| Vyberte * * myResourceGroup, který byl vytvořen dříve.|
   |Název virtuálního počítače| Zadejte *myVmPublic*|
   |Oblast | (USA) USA – východ
   |Možnosti dostupnosti| Zóna dostupnosti|
   |Zóna dostupnosti | 1 |
   |Image | Windows Server 2019 Datacenter – Gen1 |
   |Velikost | Vyberte velikost instance virtuálního počítače, kterou chcete použít. |
   |Uživatelské jméno|Zadejte libovolné uživatelské jméno.|
   |Heslo| Zadejte libovolné heslo. Heslo musí mít délku aspoň 12 znaků a musí splňovat [definované požadavky na složitost](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   |Veřejné příchozí porty | Povolení vybraných portů |
   |Vyberte příchozí porty | Ponechat výchozí nastavení na *RDP (3389)* |

   ![Výběr virtuální sítě](./media/tutorial-restrict-network-access-to-resources/virtual-machine-settings.png)
  
4. Vyberte kartu **síť** a pak vyberte **myVirtualNetwork**. 
5. Vyberte *veřejnou* podsíť.
6. V části **Skupina zabezpečení sítě síťové karty** vyberte **Upřesnit**. Portál pro vás automaticky vytvoří skupinu zabezpečení sítě, která povolí port 3389, který musí být otevřený, abyste se později mohli připojit k virtuálnímu počítači. 

   ![Zadání základních informací o virtuálním počítači](./media/tutorial-restrict-network-access-to-resources/virtual-machine-basics.png)

7. Vyberte **zkontrolovat a vytvořit** a pak **vytvořte** a počkejte na dokončení nasazení.
8. Klikněte na **Přejít k prostředku** nebo otevřete stránku **Domovská > virtuální počítače** a vyberte virtuální počítač, který jste právě vytvořili *myVmPublic*, který by se měl spustit.

### <a name="create-the-second-virtual-machine"></a>Vytvoření druhého virtuálního počítače

1. Proveďte znovu kroky 1-8, ale v kroku 3 pojmenujte virtuální počítač *myVmPrivate* a nastavte **veřejný příchozí port** na None. 
2. V kroku 4-5 vyberte **privátní** podsíť.

>[!NOTE]
> Nastavení **Skupina zabezpečení sítě** a **veřejné příchozí porty** by mělo zrcadlit bitovou kopii uvedenou níže, včetně modrého potvrzovacího okna, které uvádí: "všechny veřejné internetové přenosy budou ve výchozím nastavení blokované".

   ![Vytvořit privátní virtuální počítač](./media/tutorial-restrict-network-access-to-resources/create-private-virtual-machine.png)

3. Vyberte **zkontrolovat a vytvořit** a pak **vytvořte** a počkejte na dokončení nasazení. 

>[!WARNING]
> Nepokračujte k dalšímu kroku, dokud se nasazení nedokončí.

4. Počkejte na zobrazené okno pro potvrzení a klikněte na **Přejít k prostředku**.

   ![Vytvořit okno potvrzení privátního virtuálního počítače](./media/tutorial-restrict-network-access-to-resources/create-vm-confirmation-window.png)

## <a name="confirm-access-to-storage-account"></a>Ověření přístupu k účtu úložiště

1. Po vytvoření virtuálního počítače s *myVmPrivate* klikněte na **Přejít k prostředku**. 
2. Připojte se k virtuálnímu počítači výběrem možnosti **připojit > RDP**.
3. Po výběru tlačítka **připojit** se vytvoří soubor protokol RDP (Remote Desktop Protocol) (. RDP). Klikněte na **Stáhnout soubor RDP** a stáhněte ho do svého počítače.  
4. Otevřete stažený soubor .rdp. Pokud se zobrazí výzva, vyberte **Připojit**. Zadejte uživatelské jméno a heslo, které jste zadali při vytváření virtuálního počítače. Možná budete muset vybrat **Další možnosti** a pak **použít jiný účet** k zadání přihlašovacích údajů, které jste zadali při vytváření virtuálního počítače. V poli e-mail zadejte přihlašovací údaje "účet správce: uživatelské jméno", které jste zadali dříve. 
5. Vyberte **OK**.
6. Během procesu přihlášení se může zobrazit upozornění certifikátu. Pokud se toto upozornění zobrazí, vyberte **Ano** nebo **Pokračovat** a pokračujte v připojování. Měl by se zobrazit spuštění virtuálního počítače, jak je znázorněno na následujícím obrázku:

   ![Zobrazit privátní virtuální počítač se systémem](./media/tutorial-restrict-network-access-to-resources/virtual-machine-running.png)

7. V okně virtuálního počítače otevřete instanci rozhraní příkazového řádku PowerShellu.
8. Pomocí níže uvedeného skriptu namapujte sdílenou složku Azure na jednotku Z pomocí prostředí PowerShell. Před spuštěním příkazů, které následují, nahraďte `<storage-account-key>` a `<storage-account-name>` v obou polích hodnoty, které jste zadali, a "d výše v části [Vytvoření účtu úložiště](#create-a-storage-account).

   ```powershell
   $acctKey = ConvertTo-SecureString -String "<storage-account-key>" -AsPlainText -Force
   $credential = New-Object System.Management.Automation.PSCredential -ArgumentList "Azure\<storage-account-name>", $acctKey
   New-PSDrive -Name Z -PSProvider FileSystem -Root "\\<storage-account-name>.file.core.windows.net\my-file-share" -Credential $credential
   ```

   PowerShell vrátí podobný výstup jako v následujícím příkladu:

   ```powershell
   Name           Used (GB)     Free (GB) Provider      Root
   ----           ---------     --------- --------      ----
   Z                                      FileSystem    \\vnt.file.core.windows.net\my-f...
   ```

   Sdílená složka Azure se úspěšně namapovala na jednotku Z.

9.   Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPrivate*.

## <a name="confirm-access-is-denied-to-storage-account"></a>Ověření odepření přístupu k účtu úložiště

1. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu zadejte *myVmPublic*.
2. Jakmile se ve výsledcích hledání zobrazí virtuální počítač **myVmPublic**, vyberte ho.
3. Proveďte kroky 1-8 výše v v části [potvrzení přístupu k účtu úložiště](#confirm-access-to-storage-account) pro virtuální počítač *myVmPublic* .

   Po krátkém čekání se zobrazí chyba `New-PSDrive : Access is denied`. Přístup byl odepřen, protože virtuální počítač *myVmPublic* je nasazený v podsíti *Public*. Podsíť *Public* nemá pro Azure Storage povolen žádný koncový bod služby. Účet úložiště povoluje síťový přístup pouze z podsítě *Private*, nikoliv z podsítě *Private*.

4. Ukončete relaci vzdálené plochy k virtuálnímu počítači *myVmPublic*.
5. Zpátky v Azure Portal přejdete na účet jedinečného úložiště, který jste vytvořili dříve.
6. V části Souborová služba vyberte **sdílené** složky, složka *My-File-Shared*, kterou jste vytvořili dříve.
7. Měla by se zobrazit následující chybová zpráva:

   ![Chyba odepření přístupu](./media/tutorial-restrict-network-access-to-resources/access-denied-error.png)
   
>[!NOTE] 
> Přístup byl odepřen, protože se váš počítač nenachází v podsíti *Private* virtuální sítě *MyVirtualNetwork*.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a všechny prostředky, které obsahuje:

1. Do pole **Hledat** v horní části portálu zadejte *myResourceGroup*. Jakmile se ve výsledcích hledání zobrazí skupina prostředků **myResourceGroup**, vyberte ji.
2. Vyberte **Odstranit skupinu prostředků**.
3. V části **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** zadejte *myResourceGroup* a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili koncový bod služby pro podsíť virtuální sítě. Dozvěděli jste se, že koncové body služeb můžete povolit pro prostředky nasazené z několika služeb Azure. Vytvořili jste účet služby Azure Storage a omezili jste síťový přístup k účtu úložiště pouze na prostředky v rámci podsítě virtuální sítě. Další informace o koncových bodech služeb najdete v tématech [Přehled koncových bodů služeb](virtual-network-service-endpoints-overview.md) a [Správa podsítí](virtual-network-manage-subnet.md).

Pokud ve svém účtu máte více virtuálních sítí, možná budete chtít propojit dvě virtuální sítě, aby mezi sebou mohly komunikovat prostředky v obou virtuálních sítích. Informace o postupu propojení virtuálních sítí najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Připojení virtuálních sítí](./tutorial-connect-virtual-networks-portal.md)
