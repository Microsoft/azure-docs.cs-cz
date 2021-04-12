---
title: Konfigurace naslouchacího procesu skupiny dostupnosti & Load Balancer (Azure Portal)
description: Podrobné pokyny pro vytvoření naslouchacího procesu pro skupinu dostupnosti Always On pro SQL Server ve virtuálních počítačích Azure
services: virtual-machines
documentationcenter: na
author: MashaMSFT
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 6e53a6a4875b3dde55d1822daa342d6cde536d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100096425"
---
# <a name="configure-a-load-balancer-for-a-sql-server-always-on-availability-group-in-azure-virtual-machines"></a>Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti Always On SQL Server v Azure Virtual Machines

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Tento článek vysvětluje, jak vytvořit nástroj pro vyrovnávání zatížení pro skupinu dostupnosti Always On SQL Server v Azure Virtual Machines, která běží s Azure Resource Manager. Skupina dostupnosti vyžaduje nástroj pro vyrovnávání zatížení, pokud jsou instance SQL Server v Azure Virtual Machines. Nástroj pro vyrovnávání zatížení ukládá IP adresu pro naslouchací proces skupiny dostupnosti. Pokud skupina dostupnosti zahrnuje více oblastí, bude každá oblast potřebovat nástroj pro vyrovnávání zatížení.

K dokončení této úlohy potřebujete mít ve virtuálních počítačích Azure, které běží s Správce prostředků, nasazenou skupinu dostupnosti Always On SQL Server. Oba SQL Server virtuální počítače musí patřit do stejné skupiny dostupnosti. K automatickému vytvoření skupiny dostupnosti v Správce prostředků můžete použít [šablonu společnosti Microsoft](./availability-group-quickstart-template-configure.md) . Tato šablona automaticky vytvoří pro vás interní nástroj pro vyrovnávání zatížení. 

Pokud budete chtít, můžete [ručně nakonfigurovat skupinu dostupnosti](availability-group-manually-configure-tutorial.md).

Tento článek vyžaduje, aby byly vaše skupiny dostupnosti již nakonfigurovány.  

Zobrazit související články:

* [Konfigurace skupin dostupnosti Always On na virtuálním počítači Azure (GUI)](availability-group-manually-configure-tutorial.md)   
* [Konfigurace propojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Procházením tohoto článku vytvoříte a nakonfigurujete nástroj pro vyrovnávání zatížení v Azure Portal. Po dokončení procesu nakonfigurujete cluster tak, aby používal IP adresu z nástroje pro vyrovnávání zatížení pro naslouchací proces skupiny dostupnosti.

## <a name="create--configure-load-balancer"></a>Vytvoření & konfigurace nástroje pro vyrovnávání zatížení 

V této části úlohy proveďte následující kroky:

1. V Azure Portal vytvořte Nástroj pro vyrovnávání zatížení a nakonfigurujte IP adresu.
2. Nakonfigurujte fond back-end.
3. Vytvořte sondu. 
4. Nastavte pravidla vyrovnávání zatížení.

> [!NOTE]
> Pokud jsou instance SQL Server v několika skupinách prostředků a oblastech, proveďte každý krok dvakrát a jednou v každé skupině prostředků.
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Vytvoření nástroje pro vyrovnávání zatížení a konfigurace IP adresy

Nejdřív vytvořte Nástroj pro vyrovnávání zatížení. 

1. V Azure Portal otevřete skupinu prostředků, která obsahuje SQL Server virtuálních počítačů. 

2. Ve skupině prostředků vyberte **Přidat**.

3. Vyhledejte **Nástroj pro vyrovnávání zatížení**. Ve výsledcích hledání vyberte možnost **Load Balancer** (Publikováno **Microsoftem**).

4. V okně **Load Balancer** vyberte **vytvořit**.

5. V dialogovém okně **vytvořit nástroj pro vyrovnávání zatížení** nakonfigurujte nástroj pro vyrovnávání zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující Nástroj pro vyrovnávání zatížení. Například **sqlLB**. |
   | **Typ** |**Interní**: většina implementací používá interní nástroj pro vyrovnávání zatížení, který umožňuje aplikacím v rámci stejné virtuální sítě připojit se ke skupině dostupnosti.  </br> **Externí**: umožňuje aplikacím připojit se ke skupině dostupnosti prostřednictvím veřejného internetového připojení. |
   | **SKU** |**Basic**: výchozí možnost. Platí pouze v případě, že instance SQL Server jsou ve stejné skupině dostupnosti. </br> **Standardní**: upřednostňovaná. Platí, pokud jsou instance SQL Server ve stejné skupině dostupnosti. Vyžaduje se, pokud jsou vaše SQL Server instance v různých zónách dostupnosti. |
   | **Virtuální síť** |Vyberte virtuální síť, ve které se nacházejí instance SQL Server. |
   | **Podsíť** |Vyberte podsíť, ve které jsou instance SQL Server. |
   | **Přiřazení IP adresy** |**staticky**. |
   | **Privátní IP adresa** |Zadejte dostupnou IP adresu z podsítě. Tuto IP adresu použijte při vytváření naslouchacího procesu v clusteru. V rámci skriptu PowerShellu dále v tomto článku použijte tuto adresu pro `$ListenerILBIP` proměnnou. |
   | **Předplatné** |Pokud máte více předplatných, může se toto pole zobrazit. Vyberte předplatné, které chcete k tomuto prostředku přidružit. Obvykle se jedná o stejné předplatné, jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které jsou instance SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterém jsou instance SQL Server. |

6. Vyberte **Vytvořit**. 

Azure vytvoří nástroj pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení patří do konkrétní sítě, podsítě, skupiny prostředků a umístění. Po dokončení úlohy Azure ověřte nastavení nástroje pro vyrovnávání zatížení v Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurace fondu back-end

Azure volá *fond back*-end fondu adres back-endu. V tomto případě je fond back-end adresami dvou instancí SQL Server ve skupině dostupnosti. 

1. Ve vaší skupině prostředků vyberte nástroj pro vyrovnávání zatížení, který jste vytvořili. 

2. V **Nastavení** vyberte **back-endové fondy**.

3. V případě **back-endu fondů** vyberte **Přidat** a vytvořte fond back-endu adres. 

4. V části **název** do pole název zadejte **název back-** end fondu.

5. V části **virtuální počítače** vyberte **Přidat virtuální počítač**. 

6. V části **zvolit virtuální počítače** vyberte vybrat **skupinu dostupnosti** a pak určete skupinu dostupnosti, do které patří SQL Server virtuální počítače.

7. Po výběru skupiny dostupnosti vyberte možnost **zvolit virtuální počítače**, vyberte dva virtuální počítače, které hostují instance SQL Server ve skupině dostupnosti, a pak zvolte **Vybrat**. 

8. Výběrem **OK** zavřete okna pro **Výběr virtuálních počítačů** a **přidejte back-end fond**. 

Azure aktualizuje nastavení fondu back-endu adres. Vaše skupina dostupnosti teď má fond dvou instancí SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: vytvoření testu paměti

Sonda definuje, jak Azure ověřuje, které instance SQL Server aktuálně vlastní naslouchací proces skupiny dostupnosti. Azure sonduje službu na základě IP adresy na portu, který definujete při vytváření testu.

1. V okně **Nastavení** nástroje pro vyrovnávání zatížení vyberte **sondy stavu**. 

2. V okně **sondy stavu** vyberte **Přidat**.

3. Nakonfigurujte sondu v okně **Přidat test paměti** . Ke konfiguraci testu použijte následující hodnoty:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje test. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít libovolný dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota pro poškozený stav** |*2* |

4.  Vyberte **OK**. 

> [!NOTE]
> Ujistěte se, že port, který zadáte, je otevřený v bráně firewall obou SQL Serverch instancí. Obě instance vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [Přidání nebo úprava pravidla brány firewall](/previous-versions/orphan-topics/ws.11/cc753558(v=ws.11)). 
> 

Azure vytvoří test a pak ho použije k otestování, která instance SQL Server má naslouchací proces pro skupinu dostupnosti.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: nastavení pravidel vyrovnávání zatížení

Pravidla vyrovnávání zatížení konfigurují způsob, jakým nástroj pro vyrovnávání zatížení směruje provoz do instancí SQL Server. Pro tento nástroj pro vyrovnávání zatížení povolte přímé vrácení serveru, protože prostředek naslouchacího procesu skupiny dostupnosti má vlastní jenom jedna z těchto dvou instancí SQL Server.

1. V okně **Nastavení** nástroje pro vyrovnávání zatížení vyberte **pravidla vyrovnávání zatížení**. 

2. V okně **pravidla vyrovnávání zatížení** vyberte **Přidat**.

3. V okně **Přidat pravidla vyrovnávání zatížení** nakonfigurujte pravidlo vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název reprezentující pravidla vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Port back-endu** |*1433*. Tato hodnota se ignoruje, protože toto pravidlo používá **plovoucí IP adresu (přímá návratová hodnota serveru)**. |
   | **Sonda** |Použijte název testu, který jste vytvořili pro tento nástroj pro vyrovnávání zatížení. |
   | **Trvalost relace** |**Žádný** |
   | **Časový limit nečinnosti (minuty)** |*4* |
   | **Plovoucí IP adresa (přímá návrat ze serveru)** |**Povoleno** |

   > [!NOTE]
   > Možná se budete muset posunout dolů, aby se zobrazila všechna nastavení.
   > 

4. Vyberte **OK**. 

5. Azure nakonfiguruje pravidlo vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení teď má nakonfigurované směrování provozu do SQL Server instance, která hostuje naslouchací proces pro skupinu dostupnosti. 

V tomto okamžiku má skupina prostředků Nástroj pro vyrovnávání zatížení, který se připojuje k oběma počítačům SQL Server. Nástroj pro vyrovnávání zatížení obsahuje také IP adresu pro naslouchací proces skupiny dostupnosti Always On SQL Server, aby jeden počítač mohl reagovat na žádosti pro skupiny dostupnosti.

> [!NOTE]
> Pokud jsou vaše instance SQL Server ve dvou různých oblastech, opakujte kroky v druhé oblasti. Každá oblast vyžaduje nástroj pro vyrovnávání zatížení. 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurace clusteru pro použití IP adresy nástroje pro vyrovnávání zatížení

Dalším krokem je konfigurace naslouchacího procesu v clusteru a převedení naslouchacího procesu do režimu online. Proveďte následující kroky: 

1. Vytvořte naslouchací proces skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání. 

2. Přepněte naslouchací proces do režimu online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: vytvoření naslouchacího procesu skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání

V tomto kroku ručně vytvoříte naslouchací proces skupiny dostupnosti v Správce clusteru s podporou převzetí služeb při selhání a SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Ověření konfigurace naslouchacího procesu

Pokud jsou prostředky a závislosti clusteru správně nakonfigurovány, měli byste být schopni zobrazit naslouchací proces v SQL Server Management Studio. Chcete-li nastavit port naslouchacího procesu, proveďte následující kroky:

1. Spusťte SQL Server Management Studio a pak se připojte k primární replice.

2. Přejít na   >    >  **naslouchací procesy** skupin dostupnosti AlwaysOn vysoké dostupnosti  

    Nyní byste měli vidět název naslouchacího procesu, který jste vytvořili v Správce clusteru s podporou převzetí služeb při selhání. 

3. Klikněte pravým tlačítkem myši na název naslouchacího procesu a pak vyberte **vlastnosti**.

4. Do pole **port** zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (výchozí nastavení je 1433), a pak vyberte **OK**.

Teď máte ve virtuálních počítačích Azure spuštěnou skupinu dostupnosti v režimu Správce prostředků. 

## <a name="test-the-connection-to-the-listener"></a>Otestování připojení k naslouchacímu procesu

Otestujte připojení provedením následujících kroků:

1. Pomocí protokolu RDP (Remote Desktop Protocol) se připojte k SQL Server instanci, která je ve stejné virtuální síti, ale nevlastní repliku. Tento server může být jinou instancí SQL Server v clusteru.

2. K otestování připojení použijte nástroj **Sqlcmd** . Například následující skript vytvoří připojení **Sqlcmd** k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:

    ```console
    sqlcmd -S <listenerName> -E
    ```

Připojení SQLCMD se automaticky připojí k instanci SQL Server, která je hostitelem primární repliky. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Vytvoření IP adresy pro další skupinu dostupnosti

Každá skupina dostupnosti používá samostatný naslouchací proces. Každý naslouchací proces má svou vlastní IP adresu. Použijte stejný nástroj pro vyrovnávání zatížení, který bude obsahovat IP adresu pro další naslouchací procesy. Po vytvoření skupiny dostupnosti přidejte IP adresu do nástroje pro vyrovnávání zatížení a pak nastavte naslouchací proces.

Chcete-li do nástroje pro vyrovnávání zatížení přidat IP adresu pomocí Azure Portal, proveďte následující kroky:

1. V Azure Portal otevřete skupinu prostředků, která obsahuje nástroj pro vyrovnávání zatížení, a pak vyberte nástroj pro vyrovnávání zatížení. 

2. V části **Nastavení** vyberte **fond IP adres front-end** a pak vyberte **Přidat**. 

3. V části **Přidat IP adresu front-endu** přiřaďte název front-endu. 

4. Ověřte, zda je **virtuální síť** a **podsíť** stejná jako instance SQL Server.

5. Nastavte IP adresu pro naslouchací proces. 
   
   >[!TIP]
   >IP adresu můžete nastavit na statickou a zadat adresu, která se v podsíti aktuálně nepoužívá. Alternativně můžete nastavit IP adresu na dynamickou a uložit nový fond front-end IP adres. Když to uděláte, Azure Portal automaticky přiřadí k fondu dostupnou IP adresu. Pak můžete znovu otevřít front-end fond IP adres a změnit přiřazení na statické. 

6. Uložte IP adresu pro naslouchací proces. 

7. Přidejte sondu stavu pomocí následujících nastavení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název pro identifikaci testu.
   |**Protokol** |TCP
   |**Port** |Nepoužívaný port TCP, který musí být k dispozici na všech virtuálních počítačích. Nedá se použít pro žádný jiný účel. Žádné dva naslouchací procesy nemohou používat stejný port testu. 
   |**Interval** |Doba mezi pokusy o testování. Použijte výchozí hodnotu (5).
   |**Prahová hodnota pro poškozený stav** |Počet po sobě jdoucích prahových hodnot, které by se měly podařit před tím, než se virtuální počítač považuje za špatný.

8. Výběrem **OK** uložte test. 

9. Vytvořte pravidlo vyrovnávání zatížení. Vyberte **pravidla vyrovnávání zatížení** a pak vyberte **Přidat**.

10. Nakonfigurujte nové pravidlo vyrovnávání zatížení pomocí následujících nastavení:

    |Nastavení |Hodnota
    |:-----|:----
    |**Název** |Název, který identifikuje pravidlo vyrovnávání zatížení. 
    |**IP adresa front-endu** |Vyberte IP adresu, kterou jste vytvořili. 
    |**Protokol** |TCP
    |**Port** |Použijte port, který používají instance SQL Server. Výchozí instance používá port 1433, pokud jste ho nezměnili. 
    |**Back-endový port** |Použijte stejnou hodnotu jako **port**.
    |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancemi SQL Server. 
    |**Sonda stavu** |Vyberte test, který jste vytvořili.
    |**Trvalost relace** |Žádné
    |**Časový limit nečinnosti (minuty)** |Výchozí (4)
    |**Plovoucí IP adresa (přímá návrat ze serveru)** | Povoleno

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurace skupiny dostupnosti pro použití nové IP adresy

Pokud chcete dokončit konfiguraci clusteru, opakujte postup, který jste provedli při první skupině dostupnosti. To znamená, že nakonfigurujte [cluster tak, aby používal novou IP adresu](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po přidání IP adresy pro naslouchací proces nakonfigurujte další skupinu dostupnosti tak, že provedete následující kroky: 

1. Ověřte, že je port testu pro novou IP adresu otevřený na obou SQL Server virtuálních počítačích. 

2. [Ve Správci clusteru přidejte Klientský přístupový bod](#addcap).

3. [Nakonfigurujte prostředek IP pro skupinu dostupnosti](#congroup).

   >[!IMPORTANT]
   >Při vytváření IP adresy použijte IP adresu, kterou jste přidali do nástroje pro vyrovnávání zatížení.  

4. [Zajistěte, aby byl prostředek skupiny dostupnosti SQL Server závislý na klientském přístupovém bodu](#dependencyGroup).

5. [Nastavte prostředek klientského přístupového bodu na základě IP adresy](#listname).
 
6. [Nastavte parametry clusteru v prostředí PowerShell](#setparam).

Až nakonfigurujete skupinu dostupnosti tak, aby používala novou IP adresu, nakonfigurujte připojení ke naslouchacího procesu. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Přidat pravidlo vyrovnávání zatížení pro distribuovanou skupinu dostupnosti

Pokud se skupina dostupnosti účastní distribuované skupiny dostupnosti, nástroj pro vyrovnávání zatížení potřebuje další pravidlo. Toto pravidlo ukládá port používaný naslouchacím serverem distribuované skupiny dostupnosti.

>[!IMPORTANT]
>Tento krok platí jenom v případě, že se skupina dostupnosti účastní [distribuované skupiny dostupnosti](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na každém serveru, který je součástí distribuované skupiny dostupnosti, vytvořte příchozí pravidlo na portu TCP naslouchacího procesu naslouchacího skupiny dostupnosti. Dokumentace používá v mnoha příkladech 5022. 

1. V Azure Portal vyberte v nástroji pro vyrovnávání zatížení možnost **pravidla vyrovnávání zatížení** a pak vyberte **+ Přidat**. 

1. Vytvořte pravidlo vyrovnávání zatížení s následujícím nastavením:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název, který identifikuje pravidlo vyrovnávání zatížení pro distribuovanou skupinu dostupnosti. 
   |**IP adresa front-endu** |Jako skupinu dostupnosti použijte stejnou IP adresu front-endu.
   |**Protokol** |TCP
   |**Port** |5022 – port pro [naslouchací proces koncového bodu skupiny dostupnosti](/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups)</br> Může to být libovolný dostupný port.  
   |**Back-endový port** | 5022 – použijte stejnou hodnotu jako **port**.
   |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancemi SQL Server. 
   |**Sonda stavu** |Vyberte test, který jste vytvořili.
   |**Trvalost relace** |Žádné
   |**Časový limit nečinnosti (minuty)** |Výchozí (4)
   |**Plovoucí IP adresa (přímá návrat ze serveru)** | Povoleno

Opakujte tyto kroky pro nástroj pro vyrovnávání zatížení u ostatních skupin dostupnosti, které se účastní distribuovaných skupin dostupnosti.

Pokud máte skupinu zabezpečení sítě Azure, která omezuje přístup, ujistěte se, že pravidla povolení zahrnují:
- IP adresy back-endu SQL Server virtuálních počítačů
- Plovoucí IP adresy nástroje pro vyrovnávání zatížení pro naslouchací proces AG
- Základní IP adresa clusteru (je-li k dispozici).

## <a name="next-steps"></a>Další kroky

- [Konfigurace skupiny dostupnosti Always On SQL Server na virtuálních počítačích Azure v různých oblastech](availability-group-manually-configure-multiple-regions.md)
