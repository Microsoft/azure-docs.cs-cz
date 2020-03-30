---
title: Konfigurace naslouchacích procesů skupiny dostupnosti & vyrovnávání zatížení (portál Azure)
description: Podrobné pokyny pro vytvoření naslouchací proces pro skupinu dostupnosti vždy na sql serveru ve virtuálních počítačích Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: aefd7a55090da7f55404d6f551ab61268582ff5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096323"
---
# <a name="configure-a-load-balancer-for-an-availability-group-on-azure-sql-server-vms"></a>Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti na virtuálních počítačích Azure SQL Server
Tento článek vysvětluje, jak vytvořit nástroj pro vyrovnávání zatížení pro sql server vždy na dostupnost skupiny ve virtuálních počítačích Azure, které běží s Azure Resource Manager. Skupina dostupnosti vyžaduje nástroj pro vyrovnávání zatížení, když jsou instance SQL Serveru na virtuálních počítačích Azure. Vykladač zatížení ukládá IP adresu pro naslouchací proces skupiny dostupnosti. Pokud skupina dostupnosti zahrnuje více oblastí, každá oblast potřebuje vyrovnávání zatížení.

K dokončení tohoto úkolu, musíte mít sql server skupiny dostupnosti nasazené na virtuálních počítačích Azure, které běží s Resource Manager. Oba virtuální počítače SQL Server musí patřit do stejné skupiny dostupnosti. Pomocí šablony [Microsoftu](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) můžete automaticky vytvořit skupinu dostupnosti ve Správci prostředků. Tato šablona automaticky vytvoří interní systém vyrovnávání zatížení pro vás. 

Pokud chcete, můžete [ručně nakonfigurovat skupinu dostupnosti](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Tento článek vyžaduje, aby vaše skupiny dostupnosti jsou již nakonfigurovány.  

Mezi související témata patří:

* [Konfigurace skupin dostupnosti always on v azure vs (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurace propojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Procházením tohoto článku vytvoříte a nakonfigurujete na webu Azure Portal správce množosti. Po dokončení procesu nakonfigurujete cluster tak, aby používal adresu IP z vykladače zatížení pro naslouchací proces skupiny dostupnosti.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Vytvoření a konfigurace správce zatížení na webu Azure Portal
V této části úkolu postupujte takto:

1. Na webu Azure Portal vytvořte vyvažovač zatížení a nakonfigurujte IP adresu.
2. Nakonfigurujte fond back-end.
3. Vytvořte sondu. 
4. Nastavte pravidla vyrovnávání zatížení.

> [!NOTE]
> Pokud jsou instance serveru SQL Server ve více skupinách a oblastech prostředků, proveďte každý krok dvakrát, jednou v každé skupině prostředků.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Vytvoření rovnováhy zatížení a konfigurace IP adresy
Nejprve vytvořte vyvažovač zatížení. 

1. Na webu Azure Portal otevřete skupinu prostředků, která obsahuje virtuální počítače SQL Serveru. 

2. Ve skupině prostředků klepněte na tlačítko **Přidat**.

3. Vyhledejte **systém vyrovnávání zatížení** a ve výsledcích hledání vyberte **položku Balancer**, kterou vydává společnost **Microsoft**.

4. V okně **Balancer zatížení** klepněte na **tlačítko Vytvořit**.

5. V dialogovém okně **Vytvořit balancer** nakonfigurujte vyvažovač zatížení následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující provynační stav zatížení. Například **sqlLB**. |
   | **Typ** |**Interní**: Většina implementací používá interní nástroj pro vyrovnávání zatížení, který umožňuje aplikacím v rámci stejné virtuální sítě připojit se ke skupině dostupnosti.  </br> **Externí**: Umožňuje aplikacím připojit se ke skupině dostupnosti prostřednictvím veřejného připojení k Internetu. |
   | **Virtuální síť** |Vyberte virtuální síť, ve které se nacházejí instance serveru SQL Server. |
   | **Podsíť** |Vyberte podsíť, ve které se nacházejí instance serveru SQL Server. |
   | **Přiřazení IP adresy** |**Statické** |
   | **Privátní IP adresa** |Zadejte dostupnou adresu IP z podsítě. Tuto adresu IP použijte při vytváření naslouchací proces v clusteru. Ve skriptu prostředí PowerShell, dále v tomto `$ILBIP` článku, použijte tuto adresu pro proměnnou. |
   | **Předplatné** |Pokud máte více předplatných, může se toto pole zobrazit. Vyberte předplatné, které chcete přidružit k tomuto prostředku. Obvykle se jedná o stejné předplatné jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, ve které se nacházejí instance serveru SQL Server. |
   | **Umístění** |Vyberte umístění Azure, ve kterých se nacházejí instance SQL Serveru. |

6. Klikněte na **Vytvořit**. 

Azure vytvoří balancer. Správce zatížení patří do určité sítě, podsítě, skupiny prostředků a umístění. Po dokončení úlohy Azure ověřte nastavení vyrovnávání zatížení v Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurace fondu back-end
Azure volá back-endový *fond back-endového fondu adres*. V tomto případě back-end fond je adresy dvou instancí serveru SQL Server ve skupině dostupnosti. 

1. Ve skupině prostředků klikněte na prostředek pro vyrovnávání zatížení, které jste vytvořili. 

2. V **části Nastavení**klepněte na **položku Fondy back-end .**

3. V **back-endových fondech**klikněte na **Přidat** a vytvořte fond adres back-end. 

4. V **části Přidat back-endový fond**zadejte název back-endového fondu. **Name**

5. V části **Virtuální počítače** **klikněte**na Přidat virtuální počítač . 

6. V **části Zvolit virtuální počítače**klikněte na Vybrat sadu **dostupnosti**a zadejte sadu dostupnosti, do které virtuální počítače SQL Serveru patří.

7. Po výběru sady dostupnosti klikněte na **Vybrat virtuální počítače**, vyberte dva virtuální počítače, které jsou hostiteli instancí serveru SQL Server ve skupině dostupnosti, a potom klikněte na **Vybrat**. 

8. Klepnutím na **tlačítko OK** zavřete listy pro **volbu virtuálních počítačů**a **přidejte back-endový fond**. 

Azure aktualizuje nastavení pro fond adres back-end. Nyní má vaše dostupnost fond dvou instancí serveru SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Vytvoření sondy
Sonda definuje, jak Azure ověří, které instance serveru SQL Server aktuálně vlastní naslouchací proces skupiny dostupnosti. Azure sonduje službu na základě IP adresy na portu, který definujete při vytváření sondy.

1. V okně **Nastavení** pro vyrovnávání zatížení klepněte na **položku Sondy stavu**. 

2. V okně **Sondy stavu** klepněte na tlačítko **Přidat**.

3. Nakonfigurujte sondu v okně **Přidat sondu.** Ke konfiguraci sondy použijte následující hodnoty:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující sondu. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít libovolný dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota pro poškozený stav** |*2* |

4.  Klikněte na tlačítko **OK**. 

> [!NOTE]
> Ujistěte se, že zadaný port je otevřený na bráně firewall obou instancí serveru SQL Server. Obě instance vyžadují příchozí pravidlo pro port TCP, který používáte. Další informace naleznete v tématu [Přidání nebo úprava pravidla brány firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure vytvoří sondu a pak ji použije k testování, která instance serveru SQL Server má naslouchací proces pro skupinu dostupnosti.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Nastavení pravidel vyrovnávání zatížení
Pravidla vyrovnávání zatížení nakonfigurují způsob, jakým nástroje pro vyrovnávání zatížení směruje provoz do instancí serveru SQL Server. Pro tento nástroj pro vyrovnávání zatížení povolíte přímé vrácení serveru, protože pouze jedna ze dvou instancí serveru SQL Server vlastní prostředek posluchače skupiny dostupnosti najednou.

1. V okně **Nastavení** pro vyrovnávání zatížení klepněte na **položku Pravidla vyrovnávání zatížení**. 

2. V okně **Pravidla vyrovnávání zatížení** klepněte na tlačítko **Přidat**.

3. V okně **Přidat pravidla vyrovnávání zatížení** nakonfigurujte pravidlo vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název představující pravidla vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Back-endový port** |*1433*. Tato hodnota je ignorována, protože toto pravidlo používá **plovoucí IP (přímé vrácení serveru)**. |
   | **Sonda** |Použijte název sondy, kterou jste vytvořili pro tento výtažek zatížení. |
   | **Trvalost relace** |**Žádné** |
   | **Časový limit nečinnosti (minuty)** |*4* |
   | **Plovoucí IP (přímý návrat serveru)** |**Enabled** (Povoleno) |

   > [!NOTE]
   > Možná budete muset posunout dolů okno zobrazit všechna nastavení.
   > 

4. Klikněte na tlačítko **OK**. 
5. Azure konfiguruje pravidlo vyrovnávání zatížení. Nyní je nástroje pro vyrovnávání zatížení nakonfigurován tak, aby směroval provoz na instanci serveru SQL Server, která hostuje naslouchací proces pro skupinu dostupnosti. 

V tomto okamžiku skupina prostředků má nástroje pro vyrovnávání zatížení, který se připojuje k oběma počítačům sql serveru. Nástroje pro vyrovnávání zatížení také obsahuje IP adresu pro sql server vždy na naslouchací proces skupiny dostupnosti, takže buď počítač může reagovat na požadavky pro skupiny dostupnosti.

> [!NOTE]
> Pokud jsou instance serveru SQL Server ve dvou samostatných oblastech, opakujte kroky v jiné oblasti. Každá oblast vyžaduje vykladač zatížení. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurace clusteru tak, aby používal adresu IP pro vyrovnávání zatížení
Dalším krokem je konfigurace naslouchací proces v clusteru a převést naslouchací proces online. Udělejte toto: 

1. Vytvořte naslouchací proces skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání. 

2. Převezte posluchače do režimu online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: Vytvoření posluchače skupiny dostupnosti v clusteru s podporou převzetí služeb při selhání
V tomto kroku ručně vytvoříte naslouchací proces skupiny dostupnosti ve Správci clusterů s podporou převzetí služeb při selhání a v aplikaci SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Ověření konfigurace naslouchací proces

Pokud jsou prostředky clusteru a závislosti správně nakonfigurovány, měli byste být schopni zobrazit naslouchací proces v aplikaci SQL Server Management Studio. Chcete-li nastavit port naslouchací procesu, postupujte takto:

1. Spusťte sql server management studio a potom se připojte k primární replice.

2. Přejít **na AlwaysOn dostupnost** > **skupiny dostupnosti skupiny** > **naslouchací procesy**.  
    Nyní byste měli vidět název posluchače, který jste vytvořili ve Správci clusteru s podporou převzetí služeb při selhání. 

3. Klepněte pravým tlačítkem myši na název posluchače a potom klepněte na příkaz **Vlastnosti**.

4. Do pole **Port** zadejte číslo portu pro posluchače skupiny dostupnosti pomocí $EndpointPort, který jste použili dříve (1433 byl výchozí) a klepněte na tlačítko **OK**.

Teď máte skupinu dostupnosti ve virtuálních počítačích Azure spuštěnou v režimu Správce prostředků. 

## <a name="test-the-connection-to-the-listener"></a>Otestovat připojení k naslouchací proces
Otestujte připojení následujícím způsobem:

1. RDP na instanci serveru SQL Server, která je ve stejné virtuální síti, ale není vlastní repliku. Tento server může být další instanci serveru SQL Server v clusteru.

2. Pomocí nástroje **sqlcmd** otestujte připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchacího procesu s ověřováním systému Windows:
   
        sqlcmd -S <listenerName> -E

Připojení SQLCMD se automaticky připojí k instanci serveru SQL Server, která je hostitelem primární repliky. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Vytvoření IP adresy pro další skupinu dostupnosti

Každá skupina dostupnosti používá samostatný naslouchací proces. Každý naslouchací proces má svou vlastní IP adresu. Použijte stejný vyvyřič zatížení pro uložení IP adresy pro další posluchače. Po vytvoření skupiny dostupnosti přidejte adresu IP do správce zatížení a nakonfigurujte naslouchací proces.

Pokud chcete přidat IP adresu do vykladače zatížení pomocí portálu Azure, postupujte takto:

1. Na webu Azure Portal otevřete skupinu prostředků, která obsahuje správce zatížení, a klikněte na položku Vyrovnávání zatížení. 

2. V části **NASTAVENÍ**klepněte na **položku Front-end OVÝ fond IP adres**a potom klepněte na tlačítko **Přidat**. 

3. V části **Přidat front-endovou IP adresu**přiřaďte název front-endu. 

4. Ověřte, zda jsou **virtuální síť** a **podsíť** stejné jako instance serveru SQL Server.

5. Nastavte IP adresu pro naslouchací proces. 
   
   >[!TIP]
   >Adresu IP můžete nastavit na statickou a zadat adresu, která není aktuálně používána v podsíti. Případně můžete nastavit IP adresu na dynamickou a uložit nový front-endový fond IP adres. Když tak učiníte, portál Azure automaticky přiřadí dostupné IP adresu do fondu. Potom můžete znovu otevřít front-end ový fond IP adres a změnit přiřazení na statické. 

6. Uložte adresu IP pro naslouchací proces. 

7. Přidejte sondu stavu pomocí následujících nastavení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název k identifikaci sondy.
   |**Protokol** |TCP
   |**Port** |Nepoužívaný port TCP, který musí být k dispozici ve všech virtuálních počítačích. Nelze jej použít k žádnému jinému účelu. Žádné dva naslouchací procesy můžete použít stejný port sondy. 
   |**Interval** |Doba mezi pokusy o sondu. Použijte výchozí (5).
   |**Prahová hodnota pro poškozený stav** |Počet po sobě jdoucích prahových hodnot, které by měly selhat, než virtuální počítač je považován za není v pořádku.

8. Klepnutím na tlačítko **OK** sondu uložíte. 

9. Vytvořte pravidlo vyrovnávání zatížení. Klepněte na **položku Pravidla vyrovnávání zatížení**a potom klepněte na tlačítko **Přidat**.

10. Nakonfigurujte nové pravidlo vyrovnávání zatížení pomocí následujících nastavení:

    |Nastavení |Hodnota
    |:-----|:----
    |**Název** |Název k identifikaci pravidla vyrovnávání zatížení. 
    |**IP adresa front-endu** |Vyberte adresu IP, kterou jste vytvořili. 
    |**Protokol** |TCP
    |**Port** |Použijte port, který používají instance serveru SQL Server. Výchozí instance používá port 1433, pokud jste jej nezměnili. 
    |**Back-endový port** |Použijte stejnou hodnotu jako **Port**.
    |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancemi serveru SQL Server. 
    |**Zdravotní sonda** |Vyberte sondu, kterou jste vytvořili.
    |**Trvalost relace** |Žádný
    |**Časový limit nečinnosti (minuty)** |Výchozí hodnota (4)
    |**Plovoucí IP (přímý návrat serveru)** | Povoleno

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurace skupiny dostupnosti pro použití nové adresy IP

Chcete-li dokončit konfiguraci clusteru, opakujte kroky, které jste provedli při vytvoření první skupiny dostupnosti. To znamená nakonfigurovat [cluster tak, aby používal novou adresu IP](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po přidání adresy IP pro naslouchací proces nakonfigurujte další skupinu dostupnosti následujícím způsobem: 

1. Ověřte, zda je port sondy pro novou adresu IP otevřený na obou virtuálních počítačích serveru SQL Server. 

2. [Ve Správci clusteru přidejte klientský přístupový bod](#addcap).

3. [Nakonfigurujte prostředek IP pro skupinu dostupnosti](#congroup).

   >[!IMPORTANT]
   >Při vytváření adresy IP použijte adresu IP, kterou jste přidali do doby pro vyrovnávání zatížení.  

4. [Uvažte prostředek skupiny dostupnosti serveru SQL Server jako závislý na přístupovém bodu klienta](#dependencyGroup).

5. [V závislosti na ip adrese je prostředek klientského přístupového bodu závislý.](#listname)
 
6. [Nastavte parametry clusteru v prostředí PowerShell](#setparam).

Po konfiguraci skupiny dostupnosti pro použití nové adresy IP nakonfigurujte připojení k naslouchací proces. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Přidání pravidla vyrovnávání zatížení pro skupinu distribuovaných dostupností

Pokud se skupina dostupnosti účastní distribuované skupiny dostupnosti, potřebuje správce zatížení další pravidlo. Toto pravidlo ukládá port používaný naslouchací proces skupiny distribuované dostupnosti.

>[!IMPORTANT]
>Tento krok platí pouze v případě, že se skupina dostupnosti účastní [skupiny distribuované dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na každém serveru, který se účastní distribuované skupiny dostupnosti, vytvořte příchozí pravidlo na naslouchací proces skupiny distribuce TCP. V mnoha příkladech dokumentace používá 5022. 

1. Na webu Azure Portal klikněte na balancer a klikněte na **Pravidla vyrovnávání zatížení**a potom klikněte na **+Přidat**. 

1. Vytvořte pravidlo vyrovnávání zatížení s následujícím nastavením:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název k identifikaci pravidla vyrovnávání zatížení pro distribuovanou skupinu dostupnosti. 
   |**IP adresa front-endu** |Použijte stejnou front-endovou IP adresu jako skupinu dostupnosti.
   |**Protokol** |TCP
   |**Port** |5022 - Port pro [naslouchací proces koncového bodu skupiny distribuované dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Může být libovolný dostupný port.  
   |**Back-endový port** | 5022 - Použijte stejnou hodnotu jako **Port**.
   |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancemi serveru SQL Server. 
   |**Zdravotní sonda** |Vyberte sondu, kterou jste vytvořili.
   |**Trvalost relace** |Žádný
   |**Časový limit nečinnosti (minuty)** |Výchozí hodnota (4)
   |**Plovoucí IP (přímý návrat serveru)** | Povoleno

Opakujte tyto kroky pro vyrovnávání zatížení na jiné skupiny dostupnosti, které se účastní distribuovaných skupin dostupnosti.

Pokud omezujete přístup pomocí skupiny zabezpečení sítě Azure, ujistěte se, že pravidla povolení zahrnují ip adresy IP v back-endu SQL Server a plovoucí IP adresy nástroje pro vyrovnávání zatížení pro naslouchací proces AG a případně základní IP adresu clusteru.

## <a name="next-steps"></a>Další kroky

- [Konfigurace skupiny dostupnosti SQL Serveru always on na virtuálních počítačích Azure v různých oblastech](virtual-machines-windows-portal-sql-availability-group-dr.md)
