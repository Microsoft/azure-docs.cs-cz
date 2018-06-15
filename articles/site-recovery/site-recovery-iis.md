---
title: Replikovat vícevrstvé založené na službě IIS webové aplikace pomocí Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak k replikaci adresáře služby IIS webové farmy virtuálních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: nisoneji
ms.openlocfilehash: 1ed0184ac76b5fb3e607458559327da5e8fe90c5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29809416"
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Replikovat vícevrstvé webové aplikace využívající službu IIS

Software, aplikace je modul produktivitu v organizaci. Různé webových aplikací můžete v organizaci slouží k jiným účelům. Některé aplikace, jako je aplikací používaných pro zpracování mzdy, finanční aplikace a weby zákazníkem, může být důležité pro organizaci. Chcete-li zabránit ke snížení produktivity, je důležité pro organizaci tyto aplikace nepřetržitě nahoru a spuštěná. Je důležité nutnosti tyto aplikace konzistentní dostupnost můžete zabránit poškození brand nebo bitové kopie organizace.

Kritické webové aplikace se obvykle nastavují jako vícevrstvé aplikace: web, databázi a aplikace jsou na různých vrstev. Kromě se šíří přes různých úrovní, aplikace může také použít několik serverů v jednotlivých vrstvách provoz vyrovnávat zatížení. Kromě toho mapování mezi různými úrovněmi a na webovém serveru může být založen na statické IP adresy. Na převzetí služeb při selhání některé z těchto mapování potřeba aktualizovat, zejména v případě, že jsou nakonfigurované více webů na webovém serveru. Pokud webových aplikací používat protokol SSL, je nutné aktualizovat vazby certifikátu.

Obnovení tradiční metody, které nejsou založené na replikace zahrnovat zálohování různé konfigurační soubory, nastavení registru, vazby, vlastní komponent (COM nebo rozhraní .NET), obsah a certifikáty. Soubory jsou obnovena prostřednictvím sadu ruční kroky. Obnovení tradiční metody zálohování a ručně obnovit soubory jsou náročná, problematických a není škálovatelné. Například může snadno zapomenete k zálohování certifikátů. Po převzetí služeb při selhání se vlevo s žádný výběr ale koupit nové certifikáty pro server.

Podporuje řešení pro zotavení po havárii dobrý modelování obnovení plány pro komplexní aplikace architektury. Můžete mít také možnost přidat vlastní kroky do plánu obnovení zpracování aplikace mapování mezi vrstvami. Pokud dojde k havárii, mapování aplikací poskytuje řešení s jedním kliknutím, že snímek, který pomáhá vést k nižší RTO.

Tento článek popisuje, jak chránit webové aplikace, která je založena na Internetové informační služby (IIS) pomocí [Azure Site Recovery](site-recovery-overview.md). Článek popisuje osvědčené postupy pro replikaci třívrstvá, založené na službě IIS webovou aplikaci pro Azure, jak to provést postupu zotavení po havárii a postup převzít aplikaci do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že budete vědět, jak provést tyto úlohy:

* [Replikace virtuálního počítače do Azure](vmware-azure-tutorial.md)
* [Návrh k síti pro obnovení](site-recovery-network-design.md)
* [Provést testovací převzetí služeb Azure](site-recovery-test-failover-to-azure.md)
* [Proveďte převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory pro nasazení
Založené na službě IIS webovou aplikaci obvykle zahrnuje jednu z následujících vzory nasazení:

**Vzor nasazení 1**

Založené na službě IIS webové farmy služby s směrování (žádostí na aplikace), server se službou IIS a SQL Server.

![Diagram založené na službě IIS webové farmy služby má tři úrovně](./media/site-recovery-iis/deployment-pattern1.png)

**Vzor nasazení 2**

Založené na službě IIS webové farmy se směrováním žádostí na aplikace, služby IIS, aplikační server a SQL Server.

![Diagram založené na službě IIS webové farmy služby má čtyři vrstvy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Podpora pro obnovení lokality

V příkladech v tomto článku používáme virtuální počítače VMware s IIS 7.5 na Windows Server 2012 R2 Enterprise. Protože replikace Site Recovery není specifické pro aplikaci, doporučení v tomto článku se měl použít ve scénářích uvedené v následující tabulce a pro různé verze služby IIS.

### <a name="source-and-target"></a>Zdroj a cíl

Scénář | Sekundární lokality | To Azure
--- | --- | ---
Hyper-V | Ano | Ano
VMware | Ano | Ano
Fyzický server | Ne | Ano
Azure|Není k dispozici|Ano

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete spustit všechny služby IIS webové farmy virtuální počítače replikující se do Azure, postupujte podle pokynů v [testovací převzetí služeb při selhání do Azure ve službě Site Recovery](site-recovery-test-failover-to-azure.md).

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou chcete virtuální počítač tak, aby. Chcete-li nastavit adresu IP, přejděte na **výpočty a síť nastavení** > **CÍLOVÁ IP adresa**.

![Snímek obrazovky, který ukazuje, jak nastavit cílová IP adresa v podokně výpočetní obnovení lokality a sítě](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různé úrovně ve vícevrstvé aplikace při selhání. Sekvencování se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvé webové aplikace, dokončete kroky popsané v tématu [vytvořit plán obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidat virtuální počítače do skupin převzetí služeb při selhání
Typické vícevrstvé aplikace webové služby IIS se skládá z následujících součástí:
* A databázové vrstvy, který má SQL virtuálních počítačů.
* Webová vrstva, která se skládá ze serveru se službou IIS a aplikační vrstvy. 

Přidávání virtuálních počítačů do jiné skupiny založené na vrstvě:

1. Vytvoření plánu obnovení. Přidáte virtuální počítače vrstvy databáze v 1. skupina. To zajišťuje, že jsou virtuální počítače vrstvy databáze vypnout poslední a zapínají první.
1. Přidáte virtuální počítače vrstvy aplikace ve skupině 2. Tím se zajistí, že virtuální počítače vrstvy aplikace se zapínají po byla zapínají databázové vrstvy.
1. Přidáte virtuální počítače vrstvy webové skupiny 3. Tím se zajistí, že virtuální počítače vrstvy webové se zapínají po byla zapínají aplikační vrstvě.
1. Přidejte virtuální počítače Vyrovnávání zatížení do skupiny 4. Tím se zajistí, že virtuální počítače Vyrovnávání zatížení se zapínají po byla zapínají webovou vrstvu.

Další informace najdete v tématu [přizpůsobit plán obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Skript přidáte do plánu obnovení
Pro webové farmy IIS fungoval správně může být zapotřebí provést některé operace na virtuálních počítačích Azure post převzetí nebo během testovací převzetí služeb. Je možné automatizovat některé operace post-převzetí služeb při selhání. Můžete například aktualizaci položky DNS, změňte vazby webu nebo změnit připojovací řetězec přidáním příslušných skriptů do plánu obnovení. [Skript VMM přidat do plánu obnovení](site-recovery-how-to-add-vmmscript.md) popisuje, jak nastavit automatizované úlohy pomocí skriptu.

#### <a name="dns-update"></a>DNS update
Pokud je server DNS nakonfigurovaný pro aktualizace dynamického DNS, virtuálních počítačů obvykle aktualizovat DNS novou IP adresu při spuštění. Pokud chcete přidat na explicitní krok pro aktualizace DNS pomocí nové IP adresy virtuálních počítačů, přidejte [skript pro aktualizaci IP ve službě DNS](https://aka.ms/asr-dns-update) jako akci post-převzetí služeb při selhání u skupiny plánu obnovení.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Připojovací řetězec v souboru web.config aplikace
Připojovací řetězec Určuje databázi, která komunikuje webu. Pokud připojovací řetězec, používá název virtuálního počítače databáze, nejsou žádné další kroky potřebné post-převzetí služeb při selhání. Aplikace může automaticky komunikovat s databází. Pokud se uchovávají IP adresu pro virtuální počítač databáze, ho nemá být také nutné aktualizovat připojovací řetězec. 

Pokud připojovací řetězec odkazuje databázového virtuálního počítače pomocí IP adresy, musí se jednat o aktualizované post-převzetí služeb při selhání. Například následující body řetězec připojení k databázi se IP adresa 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Chcete-li aktualizovat připojovací řetězec v webovou vrstvu, přidejte [skript aktualizace připojení služby IIS](https://aka.ms/asr-update-webtier-script-classic) po 3. skupina v plánu obnovení.

#### <a name="site-bindings-for-the-application"></a>Vazby webu pro aplikaci
Každé lokalitě se skládá z informace o vazbě. Informace o vazbě zahrnuje typ vazby, IP adresu, kdy server služby IIS čeká na požadavky na lokalitu, číslo portu a názvy hostitelů pro tuto lokalitu. Během převzetí služeb při selhání může být potřeba aktualizovat tyto vazby, pokud dojde ke změně ve sloupci IP adresa, který je spojen s nimi.

> [!NOTE]
>
> Pokud nastavíte vazby webu na **všechny nepřiřazené**, není nutné aktualizovat tato vazba post-převzetí služeb při selhání. Navíc pokud IP adresa přidružená lokality není změněné post-převzetí služeb při selhání, nepotřebujete aktualizovat vazby webu. (Míru uchování IP adresu závisí na architektuře sítě a podsítě přidělené k lokalitám primární a obnovení. Aktualizuje nemusí být vhodný pro vaši organizaci.)

![Snímek obrazovky, který ukazuje nastavení vazbu SSL](./media/site-recovery-iis/sslbinding.png)

Pokud jste s lokalitou přidružené IP adresu, aktualizujte všechny vazby webu s novou IP adresu. Chcete-li změnit vazby webu, přidejte [IIS webové vrstvy aktualizační skript](https://aka.ms/asr-web-tier-update-runbook-classic) po 3. skupina v plánu obnovení.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizace IP adresa služby Vyrovnávání zatížení
Pokud máte se virtuální počítač směrování žádostí na aplikace, chcete-li aktualizovat na IP adresu, přidejte [směrování žádostí na aplikace služby IIS převzetí služeb při selhání skriptu](https://aka.ms/asr-iis-arrtier-failover-script-classic) po 4 skupiny.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Vazbu certifikátu SSL pro připojení HTTPS
Web může mít přidružený certifikát SSL, který pomáhá zajistit zabezpečenou komunikaci mezi webovým serverem a prohlížeče uživatele. Pokud web má připojení HTTPS a také vazbu přidružené lokality HTTPS na IP adresu serveru služby IIS s vazbou certifikátu SSL, musíte přidat novou vazbu webu pro certifikát s IP adresou v IIS virtuální počítač post-převzetí služeb při selhání.

Certifikát protokolu SSL může být vydaný pro tyto součásti:

* Plně kvalifikovaný název domény webu.
* Název serveru.
* Certifikát se zástupným znakem pro název domény.  
* IP adresu. Pokud je certifikát SSL vydaný pro adresu IP serveru IIS, jiný certifikát SSL musí být vydaný pro IP adresu serveru služby IIS na webu Azure. Další vazby SSL pro tento certifikát musí být vytvořen. Z toho důvodu nedoporučujeme používat certifikát SSL vydaný pro IP adresu. Tato možnost je méně často používá a bude brzy zastaralá v reakci na nové změny fórum certifikátu autority nebo prohlížeče.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizace závislosti mezi webovou vrstvu a aplikační vrstvě
Pokud máte závislost specifické pro aplikaci, která je založená na IP adresu virtuálních počítačů, musíte aktualizovat tuto závislost post-převzetí služeb při selhání.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. Na portálu Azure vyberte svůj trezor služeb zotavení.
2. Vyberte plán obnovení, který jste vytvořili pro webové farmy služby IIS.
3. Vyberte **testovací převzetí služeb při selhání**.
4. Chcete-li zahájit proces testovací převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5. Po sekundární prostředí, můžete provést ověření.
6. Po dokončení ověření vyčistit testovací převzetí služeb při selhání prostředí, vyberte **dokončení ověření**.

Další informace najdete v tématu [testovací převzetí služeb při selhání do Azure ve službě Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Na portálu Azure vyberte svůj trezor služeb zotavení.
1. Vyberte plán obnovení, který jste vytvořili pro webové farmy služby IIS.
1. Vyberte **převzetí služeb při selhání**.
1. Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání ve službě Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další postup
* Další informace o [replikace jiné aplikace](site-recovery-workload.md) pomocí Site Recovery.
