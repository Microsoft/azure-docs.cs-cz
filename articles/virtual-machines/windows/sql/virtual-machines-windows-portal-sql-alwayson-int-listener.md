---
title: Vytvořte naslouchací proces skupiny dostupnosti systému SQL Server na virtuálních počítačích Azure | Microsoft Docs
description: Podrobné pokyny pro vytvoření naslouchacího procesu pro skupiny dostupnosti Always On pro SQL Server na virtuálních počítačích Azure
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
ms.openlocfilehash: 0399f9ef969098216e080140a67f81725b670115
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400626"
---
# <a name="configure-a-load-balancer-for-an-always-on-availability-group-in-azure"></a>Konfigurace vyrovnávání zatížení pro skupiny dostupnosti Always On v Azure
Tento článek vysvětluje, jak vytvořit nástroj pro vyrovnávání zatížení pro skupinu dostupnosti SQL serveru Always On v Azure virtuální počítače, které jsou spuštěny pomocí Azure Resource Manageru. Skupiny dostupnosti vyžaduje nástroj pro vyrovnávání zatížení, pokud jsou instance systému SQL Server na virtuálních počítačích Azure. Nástroje pro vyrovnávání zatížení ukládá IP adresu pro naslouchací proces skupiny dostupnosti. Pokud skupinu dostupnosti zahrnuje několik oblastí, musí každá oblast Vyrovnávání zatížení.

Tuto úlohu dokončit, musíte mít skupinu dostupnosti systému SQL Server, který je nasazen na Azure virtuální počítače, které běží s Resource Managerem. Virtuální počítače systému SQL Server musí patřit do stejné skupiny dostupnosti. Můžete použít [šablony aplikace Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) pro automatické vytvoření skupiny dostupnosti ve službě Správce prostředků. Tato šablona interní nástroj pro vás automaticky vytvoří. 

Pokud dáváte přednost, můžete [ručně nakonfigurovat skupinu dostupnosti](virtual-machines-windows-portal-sql-availability-group-tutorial.md).

Tento článek vyžaduje vaše skupiny dostupnosti jsou již nakonfigurována.  

Související témata:

* [Konfigurace skupin dostupnosti Always On virtuální počítač Azure (GUI)](virtual-machines-windows-portal-sql-availability-group-tutorial.md)   
* [Nakonfigurujte připojení VNet-to-VNet s použitím Azure Resource Manageru a prostředí PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Podle s návodem v tomto článku, vytvořit a nakonfigurovat Vyrovnávání zatížení na portálu Azure. Po dokončení procesu konfigurujete cluster pro použití IP adresy z nástroje pro vyrovnávání zatížení pro naslouchací proces skupiny dostupnosti.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Vytvoření a konfigurace pro vyrovnávání zatížení na portálu Azure
V této části úlohu postupujte takto:

1. Na portálu Azure vytvořit nástroj pro vyrovnávání zatížení a konfigurace IP adresy.
2. Nakonfigurujte fond back-end.
3. Vytvořte test. 
4. Nastavte pravidla Vyrovnávání zatížení.

> [!NOTE]
> Pokud instance systému SQL Server v více skupin prostředků a oblastí, provedení každého kroku dvakrát, jednou v každé skupině prostředků.
> 
> 

### <a name="step-1-create-the-load-balancer-and-configure-the-ip-address"></a>Krok 1: Vytvoření pro vyrovnávání zatížení a konfigurace IP adresy
Nejprve vytvořte nástroje pro vyrovnávání zatížení. 

1. Na portálu Azure otevřete skupinu prostředků, která obsahuje virtuální počítače systému SQL Server. 

2. Ve skupině prostředků, klikněte na tlačítko **přidat**.

3. Vyhledejte **nástroj pro vyrovnávání zatížení** a potom ve výsledcích hledání vyberte **Vyrovnávání zatížení**, která je publikována serverem **Microsoft**.

4. Na **nástroj pro vyrovnávání zatížení** okně klikněte na tlačítko **vytvořit**.

5. V **vytvořit nástroj pro vyrovnávání zatížení** dialogové okno pole, nástroje pro vyrovnávání zatížení nakonfigurovat následujícím způsobem:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Název text představující nástroje pro vyrovnávání zatížení. Například **sqlLB**. |
   | **Typ** |**Interní**: většinu implementací použijte Vyrovnávání zatížení interní, která umožňuje aplikacím v rámci stejné virtuální síti připojit ke skupině dostupnosti.  </br> **Externí**: umožňuje aplikacím pro připojení ke skupině dostupnosti prostřednictvím veřejného Internetu připojení. |
   | **Virtuální síť** |Vyberte virtuální síť, která jsou intances systému SQL Server v. |
   | **Podsíť** |Vyberte podsíť, které jsou instance systému SQL Server v. |
   | Přiřazení IP adresy |**Statické** |
   | **Privátní IP adresa** |Zadejte dostupnou IP adresu z podsítě. Při vytváření naslouchací proces v clusteru, použijte tuto adresu IP. Ve skriptu prostředí PowerShell, dále v tomto článku, použijte tuto adresu pro `$ILBIP` proměnné. |
   | **Předplatné** |Pokud máte více předplatných, může se objevit v tomto poli. Vyberte odběr, který chcete přidružit k tomuto prostředku. Je obvykle ve stejném předplatném jako všechny prostředky pro skupinu dostupnosti. |
   | **Skupina prostředků** |Vyberte skupinu prostředků, které jsou instance systému SQL Server v. |
   | **Umístění** |Vyberte umístění Azure, které jsou instance systému SQL Server v. |

6. Klikněte na možnost **Vytvořit**. 

Azure vytvoří nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení patří do určité sítě, podsítě, skupinu prostředků a umístění. Jakmile Azure dokončí úlohu, ověřte nastavení nástroje pro vyrovnávání zatížení v Azure. 

### <a name="step-2-configure-the-back-end-pool"></a>Krok 2: Konfigurace fondu back-end
Azure volá ve fondu back-end *fond back-end*. V takovém případě je fond back-end adresy dvě instance systému SQL Server ve skupině dostupnosti. 

1. Ve skupině prostředků klikněte na nástroje pro vyrovnávání zatížení, který jste vytvořili. 

2. Na **nastavení**, klikněte na tlačítko **back-endové fondy**.

3. Na **back-endové fondy**, klikněte na tlačítko **přidat** vytvoření fondu back-end adresy. 

4. Na **přidejte fond back-end**v části **název**, zadejte název pro fond back-end.

5. V části **virtuální počítače**, klikněte na tlačítko **přidat virtuální počítač**. 

6. V části **vyberte virtuální počítače**, klikněte na tlačítko **zvolit skupinu dostupnosti**a pak zadejte dostupnost nastavit, že virtuální počítače systému SQL Server patří do.

7. Po výběru skupiny dostupnosti, klikněte na tlačítko **zvolit virtuální počítače**, vyberte dva virtuální počítače, které jsou hostiteli instance systému SQL Server ve skupině dostupnosti a pak klikněte na tlačítko **vyberte**. 

8. Klikněte na tlačítko **OK** zavřete okna pro **vyberte virtuální počítače**, a **přidejte fond back-end**. 

Azure aktualizace nastavení fondu back-end adresy. Vaše skupina dostupnosti má nyní fond dvě instance systému SQL Server.

### <a name="step-3-create-a-probe"></a>Krok 3: Vytvoření sondu
Tato kontrola definuje, jak Azure ověřuje, která z instance systému SQL Server aktuálně vlastníkem naslouchacího procesu skupiny dostupnosti. Azure sondy službu na základě IP adresy na portu, které definujete, když vytvoříte sonda.

1. V nástroji pro vyrovnávání zatížení **nastavení** okně klikněte na tlačítko **testy stavu**. 

2. Na **testy stavu** okně klikněte na tlačítko **přidat**.

3. Konfiguraci kontroly na **přidat test** okno. Použijte následující hodnoty pro konfiguraci kontroly:

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Název text představující sonda. Například **SQLAlwaysOnEndPointProbe**. |
   | **Protokol** |**TCP** |
   | **Port** |Můžete použít jakýkoli dostupný port. Například *59999*. |
   | **Interval** |*5* |
   | **Prahová hodnota špatného stavu** |*2* |

4.  Klikněte na **OK**. 

> [!NOTE]
> Ujistěte se, že je port, který zadáte otevřen v bráně firewall obou instancí systému SQL Server. Obě instance vyžadují pravidlo pro příchozí port TCP, který používáte. Další informace najdete v tématu [přidat nebo upravit pravidlo brány Firewall](http://technet.microsoft.com/library/cc753558.aspx). 
> 
> 

Azure vytvoří sondy a použije ho k testování kterou instanci systému SQL Server má naslouchací proces skupiny dostupnosti.

### <a name="step-4-set-the-load-balancing-rules"></a>Krok 4: Nastavení pravidla Vyrovnávání zatížení
Pravidla Vyrovnávání zatížení nakonfigurovat, jak nástroj pro vyrovnávání zatížení směruje provoz do instance systému SQL Server. Pro tuto službu Vyrovnávání zatížení můžete povolit přímá odpověď ze serveru, protože jenom jedna z obou instancí systému SQL Server vlastní prostředek naslouchací proces skupiny dostupnosti v čase.

1. V nástroji pro vyrovnávání zatížení **nastavení** okně klikněte na tlačítko **pravidla Vyrovnávání zatížení**. 

2. Na **pravidla Vyrovnávání zatížení** okně klikněte na tlačítko **přidat**.

3. Na **pravidla Vyrovnávání zatížení přidat** okně nakonfigurovat pravidlo Vyrovnávání zatížení. Použijte následující nastavení: 

   | Nastavení | Hodnota |
   | --- | --- |
   | **Název** |Název text představující pravidla Vyrovnávání zatížení. Například **SQLAlwaysOnEndPointListener**. |
   | **Protokol** |**TCP** |
   | **Port** |*1433* |
   | **Back-endový Port** |*1433*. Tato hodnota se ignoruje, protože toto pravidlo používá **plovoucí IP adresa (přímá odpověď ze serveru)**. |
   | **Test** |Použijte název kontroly, kterou jste vytvořili pro tuto službu Vyrovnávání zatížení. |
   | **Trvalost relace** |None |
   | Časový limit nečinnosti (minuty) |*4* |
   | **Plovoucí IP adresa (přímá odpověď ze serveru)** |povoleno |

   > [!NOTE]
   > Můžete chtít přejděte dolů v okně můžete zobrazit všechna nastavení.
   > 

4. Klikněte na **OK**. 
5. Azure nakonfiguruje pravidlo Vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení je nyní nakonfigurováno pro přesměrovat provoz na instanci systému SQL Server, který hostuje naslouchací proces skupiny dostupnosti. 

Skupina prostředků v tomto okamžiku má Vyrovnávání zatížení, která se připojuje k oba počítače systému SQL Server. Nástroje pro vyrovnávání zatížení taky obsahuje IP adresu pro SQL serveru Always On naslouchacího procesu skupiny dostupnosti, tak, aby počítač buď může reagovat na požadavky pro skupiny dostupnosti.

> [!NOTE]
> Pokud vaše instance systému SQL Server ve dvou oblastech, opakujte kroky v jiné oblasti. Každou oblast, vyžaduje nástroj pro vyrovnávání zatížení. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Konfigurace clusteru pro použití IP adresy služby Vyrovnávání zatížení
Dalším krokem je konfigurace naslouchacího procesu v clusteru a převedení naslouchací proces online. Udělejte toto: 

1. Vytvořte naslouchací proces skupiny dostupnosti v clusteru převzetí služeb při selhání. 

2. Přepněte naslouchací proces online.

### <a name="step-5-create-the-availability-group-listener-on-the-failover-cluster"></a>Krok 5: Vytvoření naslouchacího procesu skupiny dostupnosti v clusteru převzetí služeb při selhání
V tomto kroku vytvoříte ručně naslouchacího procesu skupiny dostupnosti ve Správci clusteru převzetí služeb při selhání a SQL Server Management Studio.

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

### <a name="verify-the-configuration-of-the-listener"></a>Ověřte konfiguraci naslouchacího procesu

Pokud prostředky clusteru a závislosti jsou správně nakonfigurovány, musí být schopní zobrazit naslouchací proces v aplikaci SQL Server Management Studio. Pokud chcete nastavit port naslouchacího procesu, postupujte takto:

1. Spusťte SQL Server Management Studio a připojte se k primární replice.

2. Přejděte na **AlwaysOn vysokou dostupnost** > **skupiny dostupnosti** > **naslouchací procesy skupiny dostupnosti**.  
    Teď byste měli vidět název naslouchacího procesu, který jste vytvořili ve Správci clusteru převzetí služeb při selhání. 

3. Klikněte pravým tlačítkem na název naslouchacího procesu a pak klikněte na **vlastnosti**.

4. V **Port** pole, zadejte číslo portu pro naslouchací proces skupiny dostupnosti pomocí $EndpointPort jste použili předtím (1433 se výchozí nastavení) a potom klikněte na **OK**.

Nyní máte skupinu dostupnosti v Azure virtuální počítače běžící v režimu Resource Manager. 

## <a name="test-the-connection-to-the-listener"></a>Test připojení k naslouchací proces
Testovací připojení pomocí následujícího postupu:

1. RDP instanci systému SQL Server, který je ve stejné virtuální síti, ale není vlastníkem repliky. Tento server může být jiné instance systému SQL Server v clusteru.

2. Použití **sqlcmd** nástroj k testování připojení. Například následující skript vytvoří **sqlcmd** připojení k primární replice prostřednictvím naslouchací proces s ověřováním systému Windows:
   
        sqlcmd -S <listenerName> -E

Připojení SQLCMD se automaticky připojí k instanci systému SQL Server, který je hostitelem primární repliky. 

## <a name="create-an-ip-address-for-an-additional-availability-group"></a>Vytvoření IP adresu pro skupinu dostupnosti další

Každou skupinu dostupnosti používá samostatné naslouchací proces. Každý naslouchací proces má svou vlastní IP adresu. Použijte stejný pro vyrovnávání zatížení pro uložení IP adresu pro další moduly pro naslouchání. Po vytvoření skupiny dostupnosti, přidejte IP adresu nástroji pro vyrovnávání zatížení a potom nakonfigurujte naslouchací proces.

Chcete-li přidat IP adresu nástroji pro vyrovnávání zatížení pomocí portálu Azure, postupujte takto:

1. Na portálu Azure otevřete skupinu prostředků, která obsahuje nástroje pro vyrovnávání zatížení a potom klikněte na nástroje pro vyrovnávání zatížení. 

2. V části **nastavení**, klikněte na tlačítko **fondu IP front-endu**a potom klikněte na **přidat**. 

3. V části **přidat IP adresu front-endu**, přiřadit název front-endu. 

4. Ověřte, zda **virtuální síť** a **podsíť** jsou stejné jako instance systému SQL Server.

5. Nastavte IP adresu pro naslouchací proces. 
   
   >[!TIP]
   >Můžete nastavit na statickou IP adresu a zadejte adresu, která není právě používána v podsíti. Alternativně můžete nastavit IP adresu na dynamický a uložit nový fond IP front-endu. Pokud tak učiníte, portálu Azure automaticky přiřadí dostupnou IP adresu do fondu. Pak můžete znovu otevřít front-end fond IP adres a změnit na statické přiřazení. 

6. Uložte IP adresu pro naslouchací proces. 

7. Test stavu přidáte pomocí následující nastavení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název pro identifikaci sonda.
   |**Protokol** |TCP
   |**Port** |Nepoužitého portu TCP, který musí být k dispozici na všech virtuálních počítačů. Nelze zadat pro žádným jiným účelem. Žádné dva naslouchací procesy můžete použít stejný port testu. 
   |**Interval** |Množství času mezi pokusy o sondování. Použijte výchozí nastavení (5).
   |**Prahová hodnota špatného stavu** |Počet po sobě jdoucích prahové hodnoty, které musí selhat, aby virtuální počítač není v pořádku.

8. Klikněte na tlačítko **OK** uložit test. 

9. Vytvořte pravidlo Vyrovnávání zatížení. Klikněte na tlačítko **pravidla Vyrovnávání zatížení**a potom klikněte na **přidat**.

10. Konfigurace nové pravidlo s použitím následujících nastavení Vyrovnávání zatížení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název, který identifikuje pravidlo Vyrovnávání zatížení. 
   |**Adresa IP front-endu** |Vyberte IP adresu, kterou jste vytvořili. 
   |**Protokol** |TCP
   |**Port** |Použijte port, který používáte instance systému SQL Server. Výchozí instance používá port 1433, pokud jste změnili ho. 
   |**Back-endový port** |Použít stejnou hodnotu jako **Port**.
   |**Fond back-end** |Fond, který obsahuje virtuální počítače s instancí systému SQL Server. 
   |**Test stavu** |Zvolte test, který jste vytvořili.
   |**Trvalost relace** |Žádný
   |Časový limit nečinnosti (minuty) |Výchozí (4)
   |**Plovoucí IP adresa (přímá odpověď ze serveru)** | Povoleno

### <a name="configure-the-availability-group-to-use-the-new-ip-address"></a>Nakonfigurujte skupinu dostupnosti pro použití nové IP adresy

Chcete-li dokončit konfiguraci clusteru, opakujte kroky, které jste udělali, když jste provedli první skupinu dostupnosti. To znamená, nakonfigurujte [clusteru, aby používaly nové IP adresy](#configure-the-cluster-to-use-the-load-balancer-ip-address). 

Po přidání IP adresu pro naslouchací proces, nakonfigurujte skupinu dostupnosti další následujícím způsobem: 

1. Ověřte, zda je na virtuální počítače systému SQL Server otevřen port testu pro novou IP adresu. 

2. [Ve Správci clusteru přidat klientský přístupový bod](#addcap).

3. [Konfigurace IP prostředku pro skupinu dostupnosti](#congroup).

   >[!IMPORTANT]
   >Když vytvoříte IP adresu, použijte IP adresu, kterou jste přidali do nástroje pro vyrovnávání zatížení.  

4. [Prostředek skupiny dostupnosti systému SQL Server, aby závislé na klientský přístupový bod](#dependencyGroup).

5. [Zkontrolujte přístup k bodu klienta závislá na IP adresu prostředku](#listname).
 
6. [Nastavení parametrů clusteru v prostředí PowerShell](#setparam).

Po dokončení konfigurace skupiny dostupnosti můžete použít novou IP adresu, nakonfigurujte připojení k naslouchací proces. 

## <a name="add-load-balancing-rule-for-distributed-availability-group"></a>Přidat pravidlo pro distribuovanou skupinu dostupnosti Vyrovnávání zatížení

Pokud skupinu dostupnosti se účastní distribuované skupiny dostupnosti, Vyrovnávání zatížení musí dodatečné pravidlo. Toto pravidlo uloží port je používán naslouchacího procesu skupiny dostupnosti v distribuovaných.

>[!IMPORTANT]
>Tento krok platí jenom v případě, že účastní skupiny dostupnosti [distribuovanou skupinu dostupnosti](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups). 

1. Na každém serveru, který se účastní distribuované skupině dostupnosti vytvořte pravidlo pro příchozí na distribuované naslouchacího procesu skupiny dostupnosti TCP port. V mnoha příklady dokumentace používá 5022. 

1. Na portálu Azure klikněte na nástroje pro vyrovnávání zatížení a klikněte na tlačítko **pravidla Vyrovnávání zatížení**a potom klikněte na **+ přidat**. 

1. Vytvořte pravidlo s následujícím nastavením Vyrovnávání zatížení:

   |Nastavení |Hodnota
   |:-----|:----
   |**Název** |Název, který identifikuje pravidlo pro distribuované skupině dostupnosti Vyrovnávání zatížení. 
   |**Adresa IP front-endu** |Použijte stejnou IP adresu front-endu jako skupiny dostupnosti.
   |**Protokol** |TCP
   |**Port** |5022 - port pro [naslouchací proces skupiny koncového bodu distribuované dostupnosti](http://docs.microsoft.com/sql/database-engine/availability-groups/windows/configure-distributed-availability-groups).</br> Může být jakýkoli dostupný port.  
   |**Back-endový port** | 5022 - použijte stejnou hodnotu jako **Port**.
   |**Fond back-end** |Fond, který obsahuje virtuální počítače s instancí systému SQL Server. 
   |**Test stavu** |Zvolte test, který jste vytvořili.
   |**Trvalost relace** |Žádný
   |Časový limit nečinnosti (minuty) |Výchozí (4)
   |**Plovoucí IP adresa (přímá odpověď ze serveru)** | Povoleno

Opakujte tyto kroky pro vyrovnávání zatížení na jiné skupiny dostupnosti, které se účastní distribuované dostupnosti skupiny.

## <a name="next-steps"></a>Další postup

- [Konfigurovat skupinu dostupnosti SQL serveru Always On na virtuálních počítačích, které jsou v různých oblastech Azure](virtual-machines-windows-portal-sql-availability-group-dr.md)
