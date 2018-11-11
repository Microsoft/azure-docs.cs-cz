---
title: Vytvoření naslouchacího procesu skupiny dostupnosti systému SQL Server na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Podrobné pokyny pro vytvoření naslouchacího procesu pro skupinu dostupnosti AlwaysOn pro SQL Server na virtuálních počítačích Azure
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/16/2017
ms.author: mikeray
ms.openlocfilehash: 5e665cd0bcfdea436c2f493187c5bbea756f8f09
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248298"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurace nástroje pro vyrovnávání zatížení pro skupinu dostupnosti AlwaysOn v Azure
Tento článek vysvětluje, jak vytvořit nástroj pro vyrovnávání zatížení pro skupinu dostupnosti AlwaysOn SQL serveru ve službě Azure virtual machines, které jsou spuštěny pomocí Azure Resource Manageru. Skupině dostupnosti vyžaduje nástroj pro vyrovnávání zatížení, pokud jsou instance systému SQL Server na virtuálních počítačích Azure. Nástroje pro vyrovnávání zatížení ukládá IP adresu pro naslouchací proces skupiny dostupnosti. V případě skupiny dostupnosti pokrývá více oblastí, musí každá oblast nástroj pro vyrovnávání zatížení.

Tento úkol provést, musíte mít skupinu dostupnosti systému SQL Server nasazený na Azure virtual machines, na kterých běží s Resource Managerem. Oba virtuální počítače systému SQL Server musí patřit do stejné skupiny dostupnosti. Můžete použít [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) automaticky vytvoření skupiny dostupnosti v Resource Manageru. Tato šablona automaticky vytvoří interního nástroje pro vás. 

Pokud dáváte přednost, můžete si [ruční konfigurace skupiny dostupnosti](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Tento článek vyžaduje, že vaše skupiny dostupnosti jsou už nakonfigurovaná.  

Související témata:

* [Konfigurace skupin dostupnosti AlwaysOn ve virtuálním počítači Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Konfigurace připojení typu VNet-to-VNet pomocí Azure Resource Manageru a Powershellu](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Procházením prostřednictvím tohoto článku, vytvořit a nakonfigurovat nástroj pro vyrovnávání zatížení na webu Azure Portal. Po dokončení tohoto procesu se konfigurace clusteru pro použití na IP adresu z nástroje pro vyrovnávání zatížení pro naslouchací proces skupiny dostupnosti.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Vytvoření a konfigurace nástroje pro vyrovnávání zatížení na webu Azure Portal
V této části úlohu postupujte takto:

1. Na webu Azure Portal vytvořte nástroj pro vyrovnávání zatížení a konfiguraci IP adresy.
2. Nakonfigurujte fond back-end.
3. Vytvořte test paměti. 
4. Nastavte pravidla Vyrovnávání zatížení.

> [!NOTE]
> Pokud instance systému SQL Server v několika skupin prostředků a oblasti, proveďte jednotlivé kroky dvakrát, jednou v každé skupině prostředků.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Vytvoření nástroje pro vyrovnávání zatížení a konfiguraci IP adresy
Nejprve vytvořte nástroj pro vyrovnávání zatížení. 

1. Na webu Azure Portal otevřete skupinu prostředků obsahující virtuální počítače systému SQL Server. 

2. Ve skupině prostředků, klikněte na tlačítko **přidat**.

3. Vyhledejte **nástroj pro vyrovnávání zatížení** a potom ve výsledcích hledání vyberte **Load Balancer**, která je publikována serverem **Microsoft**.

4. Na **nástroje pro vyrovnávání zatížení** okna, klikněte na tlačítko **vytvořit**.

5. V **vytvořit nástroj pro vyrovnávání zatížení** dialogové okno nástroje pro vyrovnávání zatížení nakonfigurujte následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje nástroj pro vyrovnávání zatížení. Například **sqlLB**. |
   | **Typ** |**Interní**: Většina implementací pomocí interního nástroje, které umožňuje aplikacím v rámci stejné virtuální síti připojit ke skupině dostupnosti.  </br> **Externí**: umožňuje aplikacím, aby se připojit ke skupině dostupnosti prostřednictvím veřejného Internetu. |
   | **Virtuální síť** |Vyberte virtuální síť, která jsou instance systému SQL Server. |
   | **Podsíť** |Vyberte podsíť, která instance systému SQL Server jsou v. |
   | **Přiřazení IP adresy** |**Statická** |
   | **Privátní IP adresa** |Zadejte dostupnou IP adresu z podsítě. Při vytváření naslouchacího procesu v clusteru, použijte tuto IP adresu. Tuto adresu pro použijte v skriptu prostředí PowerShell, dále v tomto článku `$ILBIP` proměnné. |
   | **Předplatné** |Pokud máte více předplatných, může se zobrazit tato pole. Vyberte předplatné, které chcete přidružit k tomuto prostředku. Je obvykle stejné předplatné jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, instance SQL serveru jsou v. |
   | **Umístění** |Vyberte oblast Azure, které jsou instance SQL serveru v. |

6. Klikněte na možnost **Vytvořit**. 

Azure vytvoří nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení patří do konkrétní sítě, podsítě, skupinu prostředků a umístění. Jakmile Azure dokončí úlohu, ověřte nastavení nástroje pro vyrovnávání zatížení v Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurace back endového fondu
Volání back endovém fondu adres Azure *back-endový fond*. V tomto případě je back endového fondu adres dvě instance systému SQL Server ve skupině dostupnosti. 

1. Ve vaší skupině prostředků klikněte na nástroj pro vyrovnávání zatížení, který jste vytvořili. 

2. Na **nastavení**, klikněte na tlačítko **back-endové fondy**.

3. Na **back-endové fondy**, klikněte na tlačítko **přidat** k vytvoření fondu back endových adres. 

4. Na **přidat back-endový fond**v části **název**, zadejte název pro back endový fond.

5. V části **virtuálních počítačů**, klikněte na tlačítko **přidat virtuální počítač**. 

6. V části **zvolením služby virtual machines**, klikněte na tlačítko **zvolte skupinu dostupnosti**a pak zadejte dostupnosti, aby virtuální počítače systému SQL Server patřit.

7. Po zvolení skupinu dostupnosti, klikněte na tlačítko **zvolením služby virtual machines**, vyberte dva virtuální počítače, které jsou hostiteli instance systému SQL Server ve skupině dostupnosti a potom klikněte na tlačítko **vyberte**. 

8. Klikněte na tlačítko **OK** zavřete okna pro **zvolením služby virtual machines**, a **přidat back-endový fond**. 

Nastavení fondu back endových adres aktualizací Azure. Vaše skupina dostupnosti má teď fond dvě instance systému SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Vytvoření testu paměti
Sonda definuje, jak Azure ověří, které z instance systému SQL Server aktuálně vlastníkem naslouchacího procesu skupiny dostupnosti. Azure sondy služba založená na IP adresu na portu, který můžete definovat při vytváření testu.

1. V nástroji pro vyrovnávání zatížení **nastavení** okna, klikněte na tlačítko **sondy stavu**. 

2. Na **sondy stavu** okna, klikněte na tlačítko **přidat**.

3. Konfigurace testu na **přidat test** okno. Konfigurace testu, použijte následující hodnoty:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje sondy. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít jakýkoli dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota špatného stavu** |*2* |

4.  Klikněte na **OK**. 

> [!NOTE]
> Ujistěte se, že je port, který zadáte otevřete v bráně firewall obě instance systému SQL Server. Obě instance vyžaduje příchozí pravidlo pro port TCP, který používáte. Další informace najdete v tématu [přidat nebo upravit pravidlo brány Firewall](https://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure vytvoří testu a použije ho k otestování kterou instanci systému SQL Server má naslouchací proces skupiny dostupnosti.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Nastavení pravidla Vyrovnávání zatížení
Pravidla Vyrovnávání zatížení nakonfigurovat, jak nástroj pro vyrovnávání zatížení směruje provoz do instancí systému SQL Server. Pro tento nástroj pro vyrovnávání zatížení povolit přímá odpověď ze serveru vrácené protože pouze jeden z obou instancí systému SQL Server vlastní prostředek naslouchacího procesu skupiny dostupnosti v čase.

1. V nástroji pro vyrovnávání zatížení **nastavení** okna, klikněte na tlačítko **pravidla Vyrovnávání zatížení**. 

2. Na **pravidla Vyrovnávání zatížení** okna, klikněte na tlačítko **přidat**.

3. Na **pravidla Vyrovnávání zatížení přidat** okně konfigurovat pravidlo Vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Textový název, který představuje pravidla Vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Back-endový Port** |*1433*. Tato hodnota se ignoruje, protože toto pravidlo používá **plovoucí IP (přímá odpověď ze serveru vrácené)**. |
   | **Test paměti** |Použijte název testu, který jste vytvořili pro tento nástroj pro vyrovnávání zatížení. |
   | **Trvalost relace** |**None** |
   | **Časový limit nečinnosti (minuty)** |*4* |
   | **Plovoucí IP adresa (přímá odpověď ze serveru vrácené)** |**Enabled** (Povoleno) |

   > [!NOTE]
   > Budete muset posunout dolů v okně zobrazíte všechna nastavení.
   > 

4. Klikněte na **OK**. 
5. Azure nakonfiguruje pravidlo Vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení je nyní nakonfigurováno pro směrování provozu do instance SQL serveru, který hostuje naslouchací proces skupiny dostupnosti. 

Skupina prostředků v tomto okamžiku má nástroj pro vyrovnávání zatížení, která se připojuje k oba počítače systému SQL Server. Nástroje pro vyrovnávání zatížení také obsahuje IP adresu SQL serveru Always On naslouchací proces skupiny dostupnosti, tak, aby buď počítač můžou reagovat na požadavky pro skupiny dostupnosti.

> [!NOTE]
> Pokud vaše instance systému SQL Server ve dvou oblastech, opakujte kroky v jiné oblasti. Každá oblast vyžaduje nástroj pro vyrovnávání zatížení. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurace clusteru použít IP adresu nástroje pro vyrovnávání zatížení
Dalším krokem je konfigurace naslouchacího procesu na cluster a přiřaďte naslouchacího procesu online. Udělejte toto: 

1. Vytvořte naslouchací proces skupiny dostupnosti na clusteru pro převzetí služeb při selhání. 

2. Přeneste naslouchacího procesu online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: Vytvoření naslouchacího procesu skupiny dostupnosti na clusteru pro převzetí služeb při selhání
V tomto kroku ručně vytvořit naslouchací proces skupiny dostupnosti v modulu Správce clusteru převzetí služeb při selhání a SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Ověřit konfiguraci naslouchacího procesu

Pokud prostředky clusteru a závislosti jsou správně nakonfigurované, je třeba zobrazit naslouchací proces v aplikaci SQL Server Management Studio. Pokud chcete nastavit na port naslouchacího procesu, postupujte takto:

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

2. Přejděte na **AlwaysOn vysokou dostupnost** > **skupin dostupnosti** > **naslouchacích procesů skupin dostupnosti**.  
    Teď byste měli vidět název naslouchacího procesu, který jste vytvořili v modulu Správce clusteru převzetí služeb při selhání. 

3. Klikněte pravým tlačítkem na název naslouchacího procesu a potom klikněte na tlačítko **vlastnosti**.

4. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti s využitím $EndpointPort jste použili dříve (1433 se výchozí hodnota) a potom klikněte na tlačítko **OK**.

Nyní máte skupinu dostupnosti v Azure virtuální počítače spuštěné v režimu Resource Manageru. 

## <a name="test-the-connection-to-the-listener"></a>Test připojení k naslouchacímu procesu
Otestujte připojení následujícím způsobem:

1. Připojení RDP k instanci systému SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. Tento server může být jiné instance systému SQL Server v clusteru.

2. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním Windows:
   
        sqlcmd -S <listenerName> -E

SQLCMD připojení se automaticky připojí k instanci systému SQL Server, který je hostitelem primární repliky. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Vytvořte IP adresu pro skupinu dostupnosti další

Každá skupina dostupnosti používá samostatné naslouchacího procesu. Každý naslouchací proces má svůj vlastní IP adresu. Použijte stejný nástroj pro vyrovnávání zatížení pro uložení IP adresu pro naslouchací procesy Další. Po vytvoření skupiny dostupnosti přidat IP adresu nástroji pro vyrovnávání zatížení a potom nakonfigurujte naslouchací proces.

Chcete-li přidat IP adresu nástroji pro vyrovnávání zatížení pomocí webu Azure portal, postupujte takto:

1. Na webu Azure Portal otevřete skupinu prostředků, který obsahuje nástroje pro vyrovnávání zatížení a potom klikněte na nástroj pro vyrovnávání zatížení. 

2. V části **nastavení**, klikněte na tlačítko **IP front-endový fond**a potom klikněte na tlačítko **přidat**. 

3. V části **přidat IP adresu frontendu**, přiřaďte název front-endu. 

4. Ověřte, že **virtuální síť** a **podsítě** jsou stejné jako instance systému SQL Server.

5. Nastavte IP adresu pro naslouchací proces. 
   
   >[!TIP]
   >Můžete nastavit na statickou IP adresu a zadejte adresu, která není v současné době nepoužívá v podsíti. Alternativně můžete nastavit na dynamickou IP adresu a uložit nové front-endového fondu IP adres. Pokud tak učiníte, na webu Azure portal automaticky přiřadí dostupnou IP adresu do fondu. Můžete znovu otevřít front-endový fond IP adres a změnit přiřazení na statické. 

6. Uložte IP adresu pro naslouchací proces. 

7. Přidáte sondu stavu pomocí následujících nastavení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název pro identifikaci sondy.
   |**Protokol** |TCP
   |**Port** |Nepoužívaný port TCP, který musí být k dispozici na všech virtuálních počítačů. Nelze použít k žádnému jinému účelu. Žádné dva naslouchací procesy můžete použít stejný port sondy. 
   |**Interval** |Množství času mezi pokusy o testování. Použijte výchozí nastavení (5).
   |**Prahová hodnota špatného stavu** |Počet po sobě jdoucích prahové hodnoty, které musí selhat, než virtuální počítač považoval za poškozený.

8. Klikněte na tlačítko **OK** uložit test paměti. 

9. Vytvořte pravidlo Vyrovnávání zatížení. Klikněte na tlačítko **pravidla Vyrovnávání zatížení**a potom klikněte na tlačítko **přidat**.

10. Konfigurace nové pravidlo s použitím následujícího nastavení Vyrovnávání zatížení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název pro identifikaci pravidlo Vyrovnávání zatížení. 
   |**Front-endové IP adresy** |Vyberte IP adresu, kterou jste vytvořili. 
   |**Protokol** |TCP
   |**Port** |Použijte port, který používáte instance systému SQL Server. Výchozí instance používá port 1433, pokud jste změnili. 
   |**Back-endový port** |Použijte stejnou hodnotu jako **Port**.
   |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancí serveru SQL Server. 
   |**Sonda stavu** |Zvolte test, který jste vytvořili.
   |**Trvalost relace** |Žádný
   |**Časový limit nečinnosti (minuty)** |Výchozí (4)
   |**Plovoucí IP adresa (přímá odpověď ze serveru vrácené)** | Povoleno

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Konfigurace skupiny dostupnosti používat novou IP adresu

K dokončení konfigurace clusteru, opakujte kroky, které jste použili při provedení první skupiny dostupnosti. To znamená, nakonfigurujte [cluster používat nové IP adresy](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po přidání IP adresu pro naslouchací proces, konfigurace skupiny dostupnosti další následujícím způsobem: 

1. Ověřte, zda je otevřít na oba virtuální počítače systému SQL Server portu sondy pro novou IP adresu. 

2. [V modulu Správce clusteru přidat klientský přístupový bod](#addcap).

3. [Konfigurace IP prostředku pro skupinu dostupnosti](#congroup).

   >[!IMPORTANT]
   >Při vytváření IP adresu použijte IP adresu, kterou jste přidali do nástroje pro vyrovnávání zatížení.  

4. [Prostředek skupiny dostupnosti systému SQL Server, aby závislé na klientský přístupový bod](#dependencyGroup).

5. [Zkontrolujte přístup k bodu klienta závislá na IP adresu prostředku](#listname).
 
6. [Nastavení parametrů clusteru v prostředí PowerShell](#setparam).

Po dokončení konfigurace skupiny dostupnosti použijte novou IP adresu nakonfigurujte připojení k naslouchacímu procesu. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Přidat pravidlo Vyrovnávání zatížení pro distribuovanou skupinu dostupnosti

Pokud skupinu dostupnosti se účastní distribuované skupiny dostupnosti, nástroj pro vyrovnávání zatížení potřebuje další pravidla. Toto pravidlo uloží port je používán naslouchacím procesem skupiny dostupnosti v distribuovaných.

>[!IMPORTANT]
>Tento krok platí pouze pokud je součástí skupiny dostupnosti [distribuovanou skupinu dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na každém serveru, který se účastní distribuované skupině dostupnosti vytvořte příchozí pravidlo na naslouchací proces skupiny dostupnosti v distribuovaných portu TCP. V mnoha příkladech dokumentace používá 5022. 

1. Na webu Azure Portal, klikněte na nástroj pro vyrovnávání zatížení a klikněte na tlačítko **pravidla Vyrovnávání zatížení**a potom klikněte na tlačítko **+ přidat**. 

1. Vytvořte pravidlo s tímto nastavením Vyrovnávání zatížení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název pro identifikaci pravidlo Vyrovnávání zatížení pro distribuované skupině dostupnosti. 
   |**Front-endové IP adresy** |Pomocí stejné IP adresy front-endu jako skupiny dostupnosti.
   |**Protokol** |TCP
   |**Port** |5022 – port [distribuované koncový bod naslouchacího procesu](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Může být jakýkoli dostupný port.  
   |**Back-endový port** | 5022 – použijte stejnou hodnotu jako **Port**.
   |**Back-endový fond** |Fond, který obsahuje virtuální počítače s instancí serveru SQL Server. 
   |**Sonda stavu** |Zvolte test, který jste vytvořili.
   |**Trvalost relace** |Žádný
   |**Časový limit nečinnosti (minuty)** |Výchozí (4)
   |**Plovoucí IP adresa (přímá odpověď ze serveru vrácené)** | Povoleno

Tento postup opakujte pro nástroj pro vyrovnávání zatížení na další, které jsou součástí skupiny dostupnosti distribuované skupiny dostupnosti.

Pokud jste omezení přístupu se skupinou zabezpečení sítě Azure, zajistěte, aby pravidla povolit zahrnují adres back-end IP adresy virtuálního počítače SQL serveru a nástroje pro vyrovnávání zatížení plovoucí IP adres pro naslouchacího procesu AG a IP adresu clusteru core Pokud je k dispozici.

## <a name="next-steps"></a>Další postup

- [Nakonfigurujte skupinu dostupnosti AlwaysOn SQL serveru na virtuálních počítačích, které jsou v různých oblastech Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
