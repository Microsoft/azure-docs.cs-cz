---
title: Nastavení zotavení po havárii pro webovou aplikaci IIS pomocí Azure Site Recovery
description: Přečtěte si, jak replikovat virtuální počítače webové farmy služby IIS pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 7a4408b54b663b2cd8abc22772ac1b799ea50de0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87083765"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Nastavení zotavení po havárii pro vícevrstvou webovou aplikaci založenou na službě IIS

Aplikační software je provozní produktivita v organizaci. Různé webové aplikace mohou v organizaci sloužit k různým účelům. Některé aplikace, jako například aplikace používané pro zpracování mezd, finanční aplikace a weby s přístupem zákazníka, můžou být pro organizaci zásadní. Aby nedošlo ke ztrátě produktivity, je důležité, aby tato aplikace byla neustále v provozu. Důležitější je, že tyto aplikace jsou stále k dispozici, což může zabránit poškození značky nebo image organizace.

Kritické webové aplikace se obvykle nastavují jako vícevrstvé aplikace: web, databáze a aplikace jsou na různých úrovních. Kromě rozmístění napříč různými úrovněmi můžou aplikace při vyrovnávání zatížení v jednotlivých vrstvách použít taky několik serverů. Kromě toho může být mapování mezi různými úrovněmi a webovým serverem založené na statických IP adresách. Při převzetí služeb při selhání je potřeba některé z těchto mapování aktualizovat, zejména v případě, že je na webovém serveru nakonfigurované víc webů. Pokud webové aplikace používají protokol TLS, je nutné aktualizovat vazby certifikátu.

Tradiční metody obnovení, které nejsou založené na replikaci, zahrnují zálohování různých konfiguračních souborů, nastavení registru, vazeb, vlastních komponent (COM nebo .NET), obsahu a certifikátů. Soubory se obnovují prostřednictvím sady ručních kroků. Tradiční metody obnovení zálohování a ruční obnovování souborů jsou náročné, náchylné k chybám a Neškálovatelné. Například je možné, že se můžete k zálohování certifikátů snadno zapomenout. Po převzetí služeb při selhání zůstanete bez výběru, ale budete moct koupit nové certifikáty pro server.

Dobré řešení pro zotavení po havárii podporuje plány obnovení modelování pro komplexní architektury aplikací. Měli byste být také schopni do plánu obnovení přidat vlastní kroky pro zpracování mapování aplikací mezi vrstvami. Pokud dojde k havárii, mapování aplikací poskytuje řešení pro zajištění jediného kliknutí, které pomáhá vést k nižším RTOům.

Tento článek popisuje, jak chránit webovou aplikaci, která je založená na Internetová informační služba (IIS) pomocí [Azure Site Recovery](site-recovery-overview.md). Tento článek se zabývá osvědčenými postupy pro replikaci webové aplikace založené na službě IIS do Azure, jak provést postup zotavení po havárii a jak převzít služby při selhání z aplikace do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že víte, jak provádět následující úlohy:

* [Replikace virtuálního počítače do Azure](vmware-azure-tutorial.md)
* [Návrh sítě pro obnovení](./concepts-on-premises-to-azure-networking.md)
* [Provedení testovacího převzetí služeb při selhání do Azure](site-recovery-test-failover-to-azure.md)
* [Provedení převzetí služeb při selhání do Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory nasazení
Webová aplikace založená na službě IIS obvykle následuje po jednom z následujících vzorů nasazení:

**Vzor nasazení 1**

Webová farma založená na službě IIS s směrováním žádostí na aplikace (ARR), serverem služby IIS a SQL Server.

![Diagram webové farmy založené na službě IIS, která má tři úrovně](./media/site-recovery-iis/deployment-pattern1.png)

**Vzor nasazení 2**

Webová farma založená na službě IIS s ARR, serverem IIS, aplikačním serverem a SQL Server.

![Diagram webové farmy založené na službě IIS, která má čtyři úrovně](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Podpora Site Recovery

V příkladech v tomto článku používáme virtuální počítače VMware se službou IIS 7,5 v systému Windows Server 2012 R2 Enterprise. Vzhledem k tomu, že Site Recovery replikace není specifická pro konkrétní aplikaci, doporučení v tomto článku se budou uplatňovat ve scénářích uvedených v následující tabulce a v různých verzích služby IIS.

### <a name="source-and-target"></a>Zdroj a cíl

Scénář | Do sekundární lokality | Do Azure
--- | --- | ---
Hyper-V | Yes | Yes
VMware | Yes | Yes
Fyzický server | No | Yes
Azure|Není k dispozici|Ano

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete začít replikovat všechny virtuální počítače webové farmy služby IIS do Azure, postupujte podle pokynů v části [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

Pokud používáte statickou IP adresu, můžete zadat IP adresu, kterou má virtuální počítač převzít. IP adresu nastavíte tak, že přejdete na **výpočty a nastavení sítě**  >  **cílová IP**adresa.

![Snímek obrazovky, který ukazuje, jak nastavit cílovou IP adresu v podokně Site Recovery výpočty a síť](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje sekvencování různých vrstev v vícevrstvé aplikaci během převzetí služeb při selhání. Sekvence pomáhá udržovat konzistenci aplikací. Při vytváření plánu obnovení pro vícevrstvé webové aplikace proveďte kroky popsané v tématu [Vytvoření plánu obnovení pomocí Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin s podporou převzetí služeb při selhání
Typická Vícevrstvá webová aplikace IIS se skládá z následujících součástí:
* Databázová vrstva, která obsahuje virtuální počítače SQL.
* Webová vrstva, která se skládá ze serveru služby IIS a aplikační vrstvy. 

Přidejte virtuální počítače do různých skupin na základě této vrstvy:

1. Vytvořte plán obnovení. Přidejte virtuální počítače databázové vrstvy do skupiny 1. Tím se zajistí, že se virtuální počítače vrstvy databáze vypnou jako poslední a napoprvé se zanesou.
1. Přidejte virtuální počítače aplikační vrstvy do skupiny 2. Tím se zajistí, že se virtuální počítače vrstvy aplikace po zastavování databázové vrstvy zanesou.
1. Přidejte virtuální počítače webové vrstvy do skupiny 3. Tím se zajistí, že se virtuální počítače webové vrstvy zanesou po zastavování aplikační vrstvy.
1. Přidejte virtuální počítače pro vyrovnávání zatížení ve skupině 4. Tím se zajistí, že se virtuální počítače pro vyrovnávání zatížení zanesou po zastavování webové vrstvy.

Další informace najdete v tématu [přizpůsobení plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Přidání skriptu do plánu obnovení
Aby webová farma služby IIS fungovala správně, možná budete muset provést některé operace na virtuálním počítači Azure po převzetí služeb při selhání nebo během testovacího převzetí služeb při selhání. Některé operace po převzetí služeb při selhání můžete automatizovat. Můžete například aktualizovat položku DNS, změnit vazbu webu nebo změnit připojovací řetězec přidáním odpovídajících skriptů do plánu obnovení. [Přidání skriptu VMM do plánu obnovení](./hyper-v-vmm-recovery-script.md) popisuje, jak nastavit automatizované úlohy pomocí skriptu.

#### <a name="dns-update"></a>Aktualizace DNS
Pokud je pro dynamickou aktualizaci DNS nakonfigurovaný DNS, virtuální počítače při jejich spuštění obvykle aktualizují DNS novou IP adresou. Pokud chcete přidat explicitní krok pro aktualizaci DNS s novými IP adresami virtuálních počítačů, přidejte [skript, který aktualizuje IP adresu v DNS](https://aka.ms/asr-dns-update) jako akci po převzetí služeb při selhání u skupin plánů obnovení.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Připojovací řetězec v web.config aplikace
Připojovací řetězec Určuje databázi, se kterou web komunikuje. Pokud připojovací řetězec přenese název databázového virtuálního počítače, po převzetí služeb při selhání už nejsou potřeba žádné další kroky. Aplikace může automaticky komunikovat s databází. Pokud je navíc IP adresa pro virtuální počítač databáze zachována, není nutné aktualizovat připojovací řetězec. 

Pokud připojovací řetězec odkazuje na virtuální počítač databáze pomocí IP adresy, je nutné aktualizovat po převzetí služeb při selhání. Například následující připojovací řetězec odkazuje na databázi s IP adresou 127.0.1.2:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
<connectionStrings>
<add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
</connectionStrings>
</configuration>
```

Chcete-li aktualizovat připojovací řetězec ve webové vrstvě, přidejte [skript aktualizace připojení služby IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) za skupinu 3 v plánu obnovení.

#### <a name="site-bindings-for-the-application"></a>Vazby webu pro aplikaci
Každá lokalita se skládá z informací o vazbě. Informace o vazbě zahrnují typ vazby, IP adresu, na které server IIS naslouchá požadavkům na lokalitu, číslo portu a názvy hostitelů pro daný web. V průběhu převzetí služeb při selhání možná budete muset aktualizovat tyto vazby, pokud dojde ke změně IP adresy, která je k nim přidružená.

> [!NOTE]
>
> Pokud nastavíte vazbu webu na **Všechny nepřiřazené**, nemusíte tuto vazbu aktualizovat po převzetí služeb při selhání. Také pokud se IP adresa přidružená k lokalitě nemění po převzetí služeb při selhání, nemusíte Aktualizovat vazbu webu. (Uchování IP adresy závisí na architektuře sítě a podsítích, které jsou přiřazené primární lokalitě a lokalitě pro obnovení. Aktualizace nemusí být proveditelná pro vaši organizaci.)

![Snímek obrazovky, který zobrazuje nastavení vazby TLS/SSL](./media/site-recovery-iis/sslbinding.png)

Pokud jste přidružili IP adresu k lokalitě, aktualizujte všechny vazby webu novou IP adresou. Chcete-li změnit vazby webu, přidejte [skript aktualizace webové vrstvy služby IIS](https://aka.ms/asr-web-tier-update-runbook-classic) za skupinu 3 v plánu obnovení.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizace IP adresy nástroje pro vyrovnávání zatížení
Pokud máte virtuální počítač s ARR, abyste aktualizovali IP adresu, přidejte po skupině 4 [skript pro převzetí služeb při selhání se službou IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) .

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>Vazba certifikátu TLS/SSL pro připojení HTTPS
Web může mít přidružený certifikát TLS/SSL, který pomáhá zajistit zabezpečenou komunikaci mezi webovým serverem a prohlížečem uživatele. Pokud má web připojení HTTPS a má přidruženou vazbu serveru HTTPS k IP adrese serveru IIS s vazbou certifikátu TLS/SSL, je nutné přidat novou vazbu webu pro certifikát s IP adresou po převzetí služeb při selhání virtuálního počítače IIS.

Certifikát TLS/SSL je možné vystavit pro tyto komponenty:

* Plně kvalifikovaný název domény webu.
* Název serveru.
* Certifikát se zástupnými znaky pro název domény.  
* IP adresa. Pokud se certifikát TLS/SSL vydává na základě IP adresy serveru IIS, musí se na webu Azure vydávat jiný certifikát TLS/SSL s IP adresou serveru IIS. Je potřeba vytvořit další vazbu TLS pro tento certifikát. Z tohoto důvodu nedoporučujeme používat certifikát TLS/SSL vydaný pro IP adresu. Tato možnost se nepoužívá méně často a bude brzy zastaralá v souladu s novými změnami certifikační autority nebo fóra prohlížeče.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizace závislosti mezi webovou vrstvou a aplikační vrstvou
Pokud máte závislost specifickou pro aplikaci, která je založená na IP adrese virtuálních počítačů, musíte tuto závislost aktualizovat po převzetí služeb při selhání.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. V Azure Portal vyberte svůj trezor Recovery Services.
2. Vyberte plán obnovení, který jste vytvořili pro webovou farmu služby IIS.
3. Vyberte **Testovací převzetí služeb při selhání**.
4. Pokud chcete spustit proces testovacího převzetí služeb při selhání, vyberte bod obnovení a virtuální síť Azure.
5. Když je sekundární prostředí nahoru, můžete provádět ověřování.
6. Po dokončení ověření pro vyčištění prostředí testovacího převzetí služeb při selhání vyberte **ověřování dokončeno**.

Další informace najdete v tématu [testování převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. V Azure Portal vyberte svůj trezor Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro webovou farmu služby IIS.
1. Vyberte **Převzetí služeb při selhání**.
1. Chcete-li spustit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace najdete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další kroky
* Další informace o [replikaci dalších aplikací](site-recovery-workload.md) pomocí Site Recovery.
