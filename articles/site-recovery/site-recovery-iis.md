---
title: Vícevrstvé aplikace webové služby IIS pomocí Azure Site Recovery replikovat | Dokumentace Microsoftu
description: Zjistěte, jak replikovat IIS webové farmy virtuálních počítačů pomocí Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: 830ced767a34302a635b9e685a2aee60c95fc81f
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920844"
---
# <a name="replicate-a-multi-tier-iis-based-web-application"></a>Replikovat vícevrstvé webové aplikace využívající službu IIS

Aplikační software je modul obchodní produktivity v organizaci. Různé webové aplikace může v organizaci slouží k jiným účelům. Některé aplikace, jako jsou aplikace pro zpracování mezd, finanční aplikace a weby určených pro zákazníky, může být nezbytné k organizaci. Aby se zabránilo ztrátě produktivitu, je důležité pro organizaci, aby tyto aplikace průběžně rychle zprovoznit. Důležitější je mají tyto aplikace stále k dispozici může zabránit poškození značku nebo image organizace.

Důležité webové aplikace jsou obvykle nastaveny jako vícevrstvých aplikací: web, databáze a aplikace jsou v různých úrovních. Kromě se šíří přes různé vrstvy, aplikace může také používají více serverů v každé úrovni k vyrovnávání zatížení provozu. Mapování mezi různými úrovněmi a na webovém serveru. Kromě toho může být založen na statické IP adresy. Na převzetí služeb při selhání některé z těchto mapování potřeba aktualizovat, zejména v případě, že jsou nakonfigurované více webů na webovém serveru. Pokud webové aplikace pomocí protokolu SSL, je nutné aktualizovat vazby certifikátu.

Obnovení tradiční metody, které nejsou založené na replikaci zahrnovat zálohování různých konfigurační soubory, nastavení registru, vazby, vlastních komponent (COM nebo rozhraní .NET), obsah a certifikáty. Soubory se dají obnovit přes sadu vyžadováno provedení ručních kroků. Metody tradiční obnovení zálohování a obnovení souborů ručně jsou náročné, problematických a není škálovatelné. Například může snadno zapomenete zálohování certifikáty. Po převzetí služeb při selhání že zůstane se žádná volba, ale kupovat nové certifikáty pro server.

Podporuje řešení pro zotavení po havárii dobré modelování obnovení plány komplexních aplikačních architektur. Musíte mít také možnost přidávat vlastní kroky do plánu obnovení ke zpracování aplikací mapování mezi vrstvami. Pokud dojde k havárii, mapování aplikací poskytují jedním kliknutím, že snímek řešení, která pomáhá vést k nižší RTO.

Tento článek popisuje, jak chránit webové aplikace, která je založena na Internetové informační služby (IIS) s použitím [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčenými postupy pro replikaci třívrstvé, založené na službě IIS webové aplikace do Azure, jak provést zotavení po havárii a jak převzít služby při selhání aplikace do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že budete vědět, jak provádět následující úlohy:

* [Replikace virtuálních počítačů do Azure](vmware-azure-tutorial.md)
* [Návrh sítě pro zotavení](site-recovery-network-design.md)
* [Provést testovací převzetí služeb do Azure](site-recovery-test-failover-to-azure.md)
* [Proveďte převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Modely nasazení
Aplikace založené na službě IIS obvykle následuje jedna z následujících vzorů nasazení:

**Model nasazení 1**

Založené na službě IIS webové farmy se žádost o směrování žádostí na aplikace, server služby IIS a SQL Server.

![Diagram, který má tři úrovně farmy webové služby IIS](./media/site-recovery-iis/deployment-pattern1.png)

**Model nasazení 2**

Založené na službě IIS webové farmy se směrováním žádostí na aplikace, server služby IIS, aplikační server a SQL Server.

![Diagram založené na službě IIS webové farmy, která má čtyři vrstvy](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Site Recovery podporu

U příkladů v tomto článku používáme virtuální počítače VMware s IIS 7.5 na Windows Server 2012 R2 Enterprise. Protože replikace Site Recovery není specifická pro aplikaci, doporučení v tomto článku předpokládá použití ve scénářích uvedené v následující tabulce a pro různé verze služby IIS.

### <a name="source-and-target"></a>Zdroj a cíl

Scénář | Do sekundární lokality | Do Azure
--- | --- | ---
Hyper-V | Ano | Ano
VMware | Ano | Ano
Fyzický server | Ne | Ano
Azure|Není k dispozici|Ano

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Ke spuštění replikace všech IIS webové farmy virtuálních počítačů do Azure, postupujte podle pokynů v [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

Pokud používáte statické IP adresy, můžete zadat IP adresu, která má virtuální počítač má provést. Pokud chcete nastavit IP adresu, přejděte na **výpočty a síť nastavení** > **CÍLOVÁ IP adresa**.

![Snímek obrazovky, který ukazuje, jak nastavit cílová IP adresa v podokně Compute obnovení lokality a sítě](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různé úrovně v vícevrstvou aplikaci při selhání. Klasifikace se usnadní zachování konzistence aplikace. Když vytvoříte plán obnovení pro vícevrstvou webovou aplikaci, dokončete kroky popsané v tématu [vytvořit plán obnovení s využitím Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidávání virtuálních počítačů do skupiny převzetí služeb při selhání
Typické vícevrstvé webové aplikace služby IIS se skládá z následujících součástí:
* Databázová vrstva, která obsahuje virtuální počítače SQL.
* Webová vrstva, která se skládá ze serveru se službou IIS a aplikační vrstvy. 

Přidávání virtuálních počítačů do jiné skupiny založené na úrovni:

1. Vytvoření plánu obnovení. Přidáte virtuální počítače úrovně databáze v 1 skupině. Tím se zajistí, že databáze úroveň virtuální počítače vypnout poslední a spouštět automaticky po první.
1. Přidáte virtuální počítače vrstvy aplikace v 2. skupina. Tím se zajistí, že aplikace vrstvy virtuálního počítače se aktivují po byly aktualizovány na úrovni databáze.
1. Přidáte virtuální počítače webové vrstvy do 3. skupina. Tím se zajistí, že virtuální počítače webové vrstvy se aktivují po se aktivují aplikační vrstvy.
1. Přidáte zatížení virtuálních počítačů ve skupině 4. Tím se zajistí, že zatížení virtuálních počítačů se aktivují po se aktivují webové vrstvy.

Další informace najdete v tématu [přizpůsobit plán obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Přidat skript do plánu obnovení
Pro webové farmy IIS fungoval správně může být nutné provést některé operace virtuálních počítačích Azure post-převzetí služeb při selhání nebo během testovacího převzetí služeb. Můžete automatizovat některé operace post-převzetí služeb při selhání. Můžete například aktualizaci položky DNS, změnit vazbu webu nebo změnit připojovací řetězec tak, že přidáte příslušných skriptů do plánu obnovení. [Skript přidáte tak nástroj VMM do plánu obnovení](site-recovery-how-to-add-vmmscript.md) popisuje, jak vytvořit automatizované úlohy pomocí skriptu.

#### <a name="dns-update"></a>Aktualizace DNS.
Pokud je nakonfigurovaný DNS pro aktualizaci dynamického serveru DNS, virtuálních počítačů obvykle aktualizovat DNS novou IP adresu při jejich spuštění. Pokud chcete přidat explicitní krok aktualizace DNS pomocí nové IP adresy virtuálních počítačů, přidejte [skript pro aktualizaci IP ve službě DNS](https://aka.ms/asr-dns-update) jako akce post-převzetí služeb při selhání na skupiny plánu obnovení.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Připojovací řetězec v souboru web.config aplikace.
Připojovací řetězec Určuje databázi, která komunikuje se službou Web. Pokud připojovací řetězec představuje název virtuálního počítače databáze, nejsou žádné další kroky potřebné post-převzetí služeb při selhání. Aplikace můžou automaticky komunikovat s databází. Navíc pokud IP adresu pro virtuální počítač databáze se uchovávají, není ji muset aktualizovat připojovací řetězec. 

Připojovací řetězec odkazuje na virtuální počítač databázi s použitím IP adresy, musí být aktualizované post-převzetí služeb při selhání. Například následující body řetězec připojení k databázi pomocí IP adres 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Chcete-li aktualizovat připojovací řetězec z webové vrstvy, přidejte [skript pro aktualizaci služby IIS připojení](https://aka.ms/asr-update-webtier-script-classic) po 3. skupina v plánu obnovení.

#### <a name="site-bindings-for-the-application"></a>Vazby webu pro aplikaci
Každý server se skládá z informace o vazbě. Informace o vazbě obsahuje typ vazby, IP adresa, jakou server služby IIS čeká na požadavky serveru, číslo portu a názvy hostitelů pro lokalitu. Během převzetí služeb při selhání může být nutné aktualizovat tyto vazby, pokud dojde ke změně IP adresy, který je spojen s nimi.

> [!NOTE]
>
> Pokud nastavíte vazby webu na **všechny nepřiřazené**, není potřeba aktualizovat tato vazba post-převzetí služeb při selhání. Navíc pokud IP adresa spojená s webem nebude změněné post-převzetí služeb při selhání, není nutné aktualizovat vazbu webu. (Zachování IP adresy závisí na architekturu sítě a podsítě přidělené k lokalitám primárními a obnovovacími. Aktualizace je nemusí být vhodná pro vaši organizaci.)

![Snímek obrazovky zobrazující nastavení vazby SSL](./media/site-recovery-iis/sslbinding.png)

Pokud přidružené IP adresu s lokalitou, aktualizujte všechny vazby webu s novou IP adresu. Chcete-li změnit vazby webu, přidejte [služby IIS Webová vrstva aktualizační skript](https://aka.ms/asr-web-tier-update-runbook-classic) po 3. skupina v plánu obnovení.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizovat IP adresu nástroje pro vyrovnávání zatížení
Pokud máte směrování žádostí na aplikace virtuální počítač, k aktualizaci IP adresy, přidejte [směrování žádostí na aplikace služby IIS převzetí služeb při selhání skriptu](https://aka.ms/asr-iis-arrtier-failover-script-classic) po 4 skupiny.

#### <a name="ssl-certificate-binding-for-an-https-connection"></a>Vazba certifikátu protokolu SSL pro připojení HTTPS
Web může mít přidružený certifikát SSL, který pomáhá zajistit zabezpečenou komunikaci mezi webovým serverem a webového prohlížeče. Pokud web má připojení HTTPS a také související webu vazbu HTTPS na IP adresu serveru služby IIS vazby certifikátu SSL, je nutné přidat nové vazby webu pro certifikát IP adresou IIS virtuálního počítače post-převzetí služeb při selhání.

Certifikát SSL může být vydaný pro tyto součásti:

* Plně kvalifikovaný název domény webu.
* Název serveru.
* Certifikát se zástupným znakem pro název domény.  
* IP adresa. Pokud je certifikát SSL vydaný pro IP adresu serveru služby IIS, jiný certifikát SSL musí být vydaný pro IP adresu serveru služby IIS na webu Azure. Další vazby SSL pro tento certifikát je potřeba vytvořit. Z tohoto důvodu nedoporučujeme používat certifikát SSL vydaný pro IP adresu. Tato možnost je méně často se využívá a brzy přestanou používat v souladu se nové změny fórum certifikátu autority a prohlížeče.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizovat závislosti mezi webovou vrstvu a aplikační vrstvy
Pokud máte specifické pro aplikaci závislostí, založené na IP adresu virtuálních počítačů, je nutné aktualizovat tuto závislost post-převzetí služeb při selhání.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
2. Vyberte plán obnovení, který jste vytvořili pro webové farmy služby IIS.
3. Vyberte **testovací převzetí služeb při selhání**.
4. Chcete-li spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5. Pokud je sekundární prostředí, můžete provést ověření.
6. Po dokončení ověření se vyčistit testovací převzetí služeb při selhání prostředí, vyberte **dokončení ověření**.

Další informace najdete v tématu [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Na webu Azure Portal vyberte svůj trezor služby Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro webové farmy služby IIS.
1. Vyberte **převzetí služeb při selhání**.
1. Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další postup
* Další informace o [replikace jiných aplikací](site-recovery-workload.md) pomocí Site Recovery.
