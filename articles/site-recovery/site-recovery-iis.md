---
title: Nastavení zotavení po havárii pro webovou aplikaci služby IIS pomocí azure site recovery
description: Zjistěte, jak replikovat virtuální počítače webové farmy služby IIS pomocí Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: dfed398124ca20771e169f6f9e7d08d4d799ee1e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478287"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-iis-based-web-application"></a>Nastavení zotavení po havárii pro vícevrstvou webovou aplikaci založenou na službě IIS

Aplikační software je motorem produktivity podnikání v organizaci. Různé webové aplikace mohou sloužit různým účelům v organizaci. Některé aplikace, například aplikace používané pro zpracování mezd, finanční aplikace a weby orientované na zákazníky, mohou být pro organizaci kritické. Aby se zabránilo ztrátě produktivity, je důležité, aby organizace měla tyto aplikace neustále v provozu. Ještě důležitější je, že tyto aplikace jsou konzistentně dostupné, může pomoci zabránit poškození značky nebo image organizace.

Kritické webové aplikace jsou obvykle nastaveny jako vícevrstvé aplikace: web, databáze a aplikace jsou na různých úrovních. Kromě toho, že jsou rozloženy na různé úrovně, aplikace mohou také používat více serverů v každé vrstvě k vyrovnávání zatížení provozu. Mapování mezi různými vrstvami a na webovém serveru může být navíc založeno na statických adresách IP. Při převzetí služeb při selhání je třeba aktualizovat některá z těchto mapování, zejména pokud je na webovém serveru nakonfigurováno více webů. Pokud webové aplikace používají protokol TLS, je nutné aktualizovat vazby certifikátů.

Tradiční metody obnovení, které nejsou založeny na replikaci, zahrnují zálohování různých konfiguračních souborů, nastavení registru, vazeb, vlastních součástí (COM nebo .NET), obsahu a certifikátů. Soubory jsou obnoveny pomocí sady ručních kroků. Tradiční metody obnovení zálohování a ruční obnovení souborů jsou těžkopádné, náchylné k chybám a nelze škálovat. Můžete například snadno zapomenout zálohovat certifikáty. Po převzetí služeb při selhání vám nezbývá nic jiného než koupit nové certifikáty pro server.

Dobré řešení zotavení po havárii podporuje plány obnovení modelování pro složité architektury aplikací. Měli byste být také schopni přidat vlastní kroky do plánu obnovení pro zpracování mapování aplikací mezi vrstvami. Pokud dojde k havárii, mapování aplikací poskytují jedno kliknutí, sure-shot řešení, které pomáhá vést k nižší RTO.

Tento článek popisuje, jak chránit webovou aplikaci založenou na Internetové informační službě (IIS) pomocí [Azure Site Recovery](site-recovery-overview.md). Článek popisuje osvědčené postupy pro replikaci třívrstvé webové aplikace založené na službě IIS do Azure, jak provést cvičení pro zotavení po havárii a jak přepojít aplikaci do Azure.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že víte, jak provést následující úkoly:

* [Replikace virtuálního počítače do Azure](vmware-azure-tutorial.md)
* [Návrh obnovovací sítě](site-recovery-network-design.md)
* [Proveďte testovací převzetí služeb při selhání v Azure](site-recovery-test-failover-to-azure.md)
* [Převzetí služeb při selhání v Azure](site-recovery-failover.md)
* [Replikace řadiče domény](site-recovery-active-directory.md)
* [Replikace SQL Serveru](site-recovery-sql.md)

## <a name="deployment-patterns"></a>Vzory nasazení
Webová aplikace založená na službě IIS obvykle sleduje jeden z následujících vzorů nasazení:

**Vzor nasazení 1**

Webová farma založená na službě IIS se směrováním požadavků na aplikace (ARR), serverem služby IIS a serverem SQL Server.

![Diagram webové farmy založené na službě IIS, která má tři úrovně](./media/site-recovery-iis/deployment-pattern1.png)

**Vzor nasazení 2**

Webová farma založená na službě IIS s arr, serverem služby IIS, aplikačním serverem a serverem SQL Server.

![Diagram webové farmy založené na službě IIS, která má čtyři úrovně](./media/site-recovery-iis/deployment-pattern2.png)

## <a name="site-recovery-support"></a>Podpora Site Recovery

Příklady v tomto článku používáme virtuální počítače VMware se službou IIS 7.5 v systému Windows Server 2012 R2 Enterprise. Vzhledem k tomu, že replikace obnovení sítě není specifická pro aplikaci, očekává se, že doporučení v tomto článku budou platit ve scénářích uvedených v následující tabulce a pro různé verze služby IIS.

### <a name="source-and-target"></a>Zdroj a cíl

Scénář | Do sekundární lokality | Do Azure
--- | --- | ---
Hyper-V | Ano | Ano
VMware | Ano | Ano
Fyzický server | Ne | Ano
Azure|Není k dispozici|Ano

## <a name="replicate-virtual-machines"></a>Replikace virtuálních počítačů

Pokud chcete začít replikovat všechny virtuální počítače webové farmy Služby IIS do Azure, postupujte podle pokynů v [části Test převzetí služeb při selhání azure v site recovery](site-recovery-test-failover-to-azure.md).

Pokud používáte statickou ADRESU IP, můžete zadat adresu IP, kterou má virtuální počítač převzít. Chcete-li nastavit adresu IP, přejděte na **položku Nastavení výpočetních prostředků a sítě** > **TARGET IP**.

![Snímek obrazovky, který ukazuje, jak nastavit cílovou IP adresu v podokně Výpočetní prostředky pro obnovení lokality a síť](./media/site-recovery-active-directory/dns-target-ip.png)

## <a name="create-a-recovery-plan"></a>Vytvoření plánu obnovení
Plán obnovení podporuje řazení různých vrstev ve vícevrstvé aplikaci během převzetí služeb při selhání. Sekvencování pomáhá udržovat konzistenci aplikace. Při vytváření plánu obnovení pro vícevrstvou webovou aplikaci proveďte kroky popsané v [části Vytvoření plánu obnovení pomocí obnovení webu](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Přidání virtuálních počítačů do skupin s podporou převzetí služeb při selhání
Typická vícevrstvá webová aplikace služby IIS se skládá z následujících součástí:
* Databázová vrstva, která má virtuální počítače SQL.
* Webová vrstva, která se skládá ze serveru Služby IIS a aplikační vrstvy. 

Přidejte virtuální počítače do různých skupin na základě úrovně:

1. Vytvořte plán obnovení. Přidejte virtuální počítače databázové vrstvy do skupiny 1. Tím zajistíte, že virtuální počítače vrstvy databáze jsou vypnuty jako poslední a nejprve vyvolány.
1. Přidejte virtuální počítače aplikační vrstvy do skupiny 2. Tím zajistíte, že virtuální počítače aplikační vrstvy jsou vyvolány po databázové vrstvy byla vyvolána.
1. Přidejte virtuální počítače webové vrstvy ve skupině 3. Tím zajistíte, že virtuální počítače webové vrstvy jsou vyvolány po vyvolání aplikační vrstvy.
1. Přidejte virtuální počítače pro vyrovnávání zatížení ve skupině 4. Tím zajistíte, že virtuální počítače vyrovnávání zatížení jsou vyvolány po webovou vrstvu byla vyvolána.

Další informace naleznete [v tématu Přizpůsobení plánu obnovení](site-recovery-runbook-automation.md#customize-the-recovery-plan).


### <a name="add-a-script-to-the-recovery-plan"></a>Přidání skriptu do plánu obnovení
Aby webová farma služby IIS fungovala správně, může být nutné provést některé operace na virtuálních počítačích Azure po převzetí služeb při selhání nebo během převzetí služeb při selhání testu. Můžete automatizovat některé operace po převzetí služeb při selhání. Můžete například aktualizovat položku DNS, změnit vazbu sítě nebo změnit připojovací řetězec přidáním odpovídajících skriptů do plánu obnovení. [Přidání skriptu VMM do plánu obnovení](site-recovery-how-to-add-vmmscript.md) popisuje, jak nastavit automatické úlohy pomocí skriptu.

#### <a name="dns-update"></a>Aktualizace DNS
Pokud je služba DNS nakonfigurována pro dynamickou aktualizaci DNS, virtuální počítače obvykle aktualizují DNS s novou adresou IP při spuštění. Pokud chcete přidat explicitní krok k aktualizaci SLUŽBY DNS s novými IP adresami virtuálních počítačů, přidejte [skript pro aktualizaci IP adresy v DNS](https://aka.ms/asr-dns-update) jako akci po převzetí služeb při selhání u skupin plánu obnovení.  

#### <a name="connection-string-in-an-applications-webconfig"></a>Připojovací řetězec v souboru web.config aplikace
Připojovací řetězec určuje databázi, se kterou web komunikuje. Pokud připojovací řetězec nese název virtuálního počítače databáze, jsou potřeba žádné další kroky po převzetí služeb při selhání. Aplikace může automaticky komunikovat s databází. Také pokud ip adresa pro databázový virtuální počítač je zachována, není nutné aktualizovat připojovací řetězec. 

Pokud připojovací řetězec odkazuje na databázový virtuální počítač pomocí IP adresy, je třeba jej aktualizovat po převzetí služeb při selhání. Například následující připojovací řetězec odkazuje na databázi s IP adresou 127.0.1.2:

        <?xml version="1.0" encoding="utf-8"?>
        <configuration>
        <connectionStrings>
        <add name="ConnStringDb1" connectionString="Data Source= 127.0.1.2\SqlExpress; Initial Catalog=TestDB1;Integrated Security=False;" />
        </connectionStrings>
        </configuration>

Chcete-li aktualizovat připojovací řetězec ve webové vrstvě, přidejte [skript aktualizace připojení služby IIS](https://gallery.technet.microsoft.com/Update-IIS-connection-2579aadc) za skupinu 3 v plánu obnovení.

#### <a name="site-bindings-for-the-application"></a>Vazby webu pro aplikaci
Každá stránka se skládá z závazných informací. Informace o vazbě zahrnují typ vazby, adresu IP, na které server služby IIS naslouchá požadavkům na web, číslo portu a názvy hostitelů pro web. Během převzetí služeb při selhání může být nutné aktualizovat tyto vazby, pokud dojde ke změně ip adresy, která je s nimi spojena.

> [!NOTE]
>
> Pokud nastavíte vazbu webu na **všechny nepřiřazené**, není nutné aktualizovat tuto vazbu po převzetí služeb při selhání. Pokud se ip adresa přidružená k webu nezmění po převzetí služeb při selhání, není nutné vazbu webu aktualizovat. (Uchovávání ip adresy závisí na architektuře sítě a podsítích přiřazených primárním lokalitám a lokalitám pro obnovení. Jejich aktualizace nemusí být pro vaši organizaci proveditelná.)

![Snímek obrazovky, který zobrazuje nastavení vazby TLS/SSL](./media/site-recovery-iis/sslbinding.png)

Pokud jste přidružili IP adresu k webu, aktualizujte všechny vazby webu s novou IP adresou. Chcete-li změnit vazby webu, přidejte [skript aktualizace webové vrstvy služby IIS](https://aka.ms/asr-web-tier-update-runbook-classic) za skupinu 3 v plánu obnovení.

#### <a name="update-the-load-balancer-ip-address"></a>Aktualizace IP adresy pro vyrovnávání zatížení
Pokud máte virtuální počítač ARR, chcete-li aktualizovat adresu IP, přidejte [skript pro převzetí služeb při selhání Služby IIS ARR](https://aka.ms/asr-iis-arrtier-failover-script-classic) za skupinu 4.

#### <a name="tlsssl-certificate-binding-for-an-https-connection"></a>Vazba certifikátu TLS/SSL pro připojení HTTPS
Web může mít přidružený certifikát TLS/SSL, který pomáhá zajistit zabezpečenou komunikaci mezi webovým serverem a prohlížečem uživatele. Pokud má web připojení HTTPS a má také přidruženou vazbu na web HTTPS na IP adresu serveru IIS se vazbou certifikátu TLS/SSL, je nutné přidat novou vazbu na web pro certifikát s IP adresou virtuálního počítače služby IIS po převzetí služeb při selhání.

Certifikát TLS/SSL lze vystavit proti těmto součástem:

* Plně kvalifikovaný název domény webu.
* Název serveru.
* Zástupný certifikát pro název domény.  
* IP adresa. Pokud je certifikát TLS/SSL vydán proti IP adrese serveru IIS, je třeba vystavit jiný certifikát TLS/SSL proti IP adrese serveru IIS na webu Azure. Je třeba vytvořit další vazbu TLS pro tento certifikát. Z tohoto důvodu doporučujeme nepoužívat certifikát TLS/SSL vydaný proti adrese IP. Tato možnost je méně široce používána a brzy bude zastaralá v souladu s novými změnami certifikační autority / fóra prohlížeče.

#### <a name="update-the-dependency-between-the-web-tier-and-the-application-tier"></a>Aktualizace závislosti mezi webovou vrstvou a aplikační vrstvou
Pokud máte závislost specifickou pro aplikaci, která je založena na IP adrese virtuálních počítačů, musíte tuto závislost po převzetí služeb při selhání aktualizovat.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

1. Na webu Azure Portal vyberte trezor služby Recovery Services.
2. Vyberte plán obnovení, který jste vytvořili pro webovou farmu služby IIS.
3. Vyberte **Testovací převzetí služeb při selhání**.
4. Chcete-li spustit proces převzetí služeb při selhání testu, vyberte bod obnovení a virtuální síť Azure.
5. Když je sekundární prostředí nahoru, můžete provést ověření.
6. Po dokončení ověření, chcete-li vyčistit testovací prostředí převzetí služeb při selhání, vyberte **ověření dokončena**.

Další informace najdete [v tématu Test převzetí služeb při selhání na Azure v site recovery](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Spuštění převzetí služeb při selhání

1. Na webu Azure Portal vyberte trezor služby Recovery Services.
1. Vyberte plán obnovení, který jste vytvořili pro webovou farmu služby IIS.
1. Vyberte **Převzetí služeb při selhání**.
1. Chcete-li zahájit proces převzetí služeb při selhání, vyberte bod obnovení.

Další informace naleznete v tématu [Převzetí služeb při selhání v tématu Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [replikaci jiných aplikací](site-recovery-workload.md) pomocí site recovery.
