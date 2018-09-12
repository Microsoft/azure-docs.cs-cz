---
title: Místní brány dat | Dokumentace Microsoftu
description: Je nutné v případě, že váš server Analysis Services v Azure se připojí k místním zdrojům dat místní bránou.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9ed2614969952eab90dfec572d9b61071981c4bf
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391619"
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Připojení k místním zdrojům dat s využitím Azure na místní bránu dat
Místní brána dat funguje jako most poskytující zabezpečený přenos dat mezi místním zdrojům dat a vaše servery Azure Analysis Services v cloudu. Kromě práce s více servery služby Azure Analysis Services ve stejné oblasti, nejnovější verzi brány také funguje s Azure Logic Apps, Power BI, Powerapps a Microsoft Flow. Více služeb ve stejném předplatném a stejné oblasti můžete přidružit jednu bránu. 

Získání první nastavení brány je složené ze čtyř částí procesu:

- **Stáhněte si a spusťte instalační program** – tento krok nainstaluje služba brány na počítači ve vaší organizaci. Také přihlásit k Azure pomocí účtu ve vaší [tenanta](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) Azure AD. Azure B2B účty (Host) nejsou podporovány.

- **Mohli bránu registrovat** – v tomto kroku, zadejte název a obnovení klíče pro bránu a vyberte oblast, bránu zaregistrovali na cloudové službě brány. Váš prostředek brány mohou být registrovány v libovolné oblasti, ale doporučujeme, aby že měla být ve stejné oblasti jako váš Server Analysis Services. 

- **Vytváření prostředku brány v Azure** – v tomto kroku vytvoříte prostředek brány ve vašem předplatném Azure.

- **Připojte své servery k prostředku brány** – Jakmile se prostředek brány v rámci vašeho předplatného, můžete začít k němu připojuje vaše servery. Pokud jsou ve stejném předplatném a stejné oblasti se můžete připojit víc serverů a dalších prostředků.

Abyste mohli hned začít, najdete v článku [instalace a konfigurace místní brány dat](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Jak to funguje
Nainstalujte do počítače ve vaší organizaci brána běží jako služba Windows **On-premises data gateway**. Tuto službu je zaregistrován Cloudová služba brány přes Azure Service Bus. Potom vytvořte prostředek brány Cloudová služba brány pro vaše předplatné Azure. Vaše servery Azure Analysis Services jsou připojena k prostředku brány. Pokud modely na serveru musí připojovat k místním datům zdroje pro dotazy nebo zpracování, prochází skrz prostředku brány, Azure Service Bus, službu místní brány dat a zdrojů dat tok dotazy a data. 

![Jak to funguje](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Dotazy a tok dat:

1. Vytvoří dotaz je Cloudová služba s zašifrované přihlašovací údaje pro zdroj dat místní. Pak zasláním do fronty pro zpracování branou.
2. Cloudová služba brány dotaz analyzuje a pošle žádost do [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Místní brána dat dotazuje Azure Service Bus na čekající žádosti.
4. Brána získá dotaz, dešifruje přihlašovací údaje a připojí ke zdrojům dat pomocí těchto přihlašovacích údajů.
5. Brána odešle dotaz na zdroj dat pro spuštění.
6. Výsledky se odesílají ze zdroje dat zpět do brány a potom do cloudové služby a vaším serverem.

## <a name="windows-service-account"> </a>Účet služby Windows
Místní brána dat je nakonfigurován na použití *NT SERVICE\PBIEgwService* přihlašovací údaje služby Windows. Ve výchozím nastavení má právo přihlášení jako služba, v kontextu počítače, na kterém bránu instalujete. Tento přihlašovací údaj se stejný účet pro připojení k místním zdrojům dat nebo účtu Azure.  

Pokud narazíte na problémy s proxy serverem způsobené ověřováním, můžete změnit účet služby Windows uživatele domény nebo účet spravované služby.

## <a name="ports"> </a>Porty
Brána vytvoří odchozí připojení ke službě Azure Service Bus. Komunikuje na odchozích portech: TCP 443 (výchozí), 5671, 5672, 9350 až 9354.  Brána nevyžaduje příchozí porty.

Doporučujeme povolit IP adresy pro vaši oblast v bráně firewall. Můžete stáhnout [seznam IP adres Datacentra Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Tento seznam se každý týden aktualizuje.

> [!NOTE]
> Uvedené v seznamu IP adres Azure Datacenter IP adresy jsou v zápisu CIDR. Další informace najdete v tématu [Classless Inter-Domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Následují plně kvalifikované názvy domény používá brána.

| Názvy domén | Odchozí porty | Popis |
| --- | --- | --- |
| *. powerbi.com |80 |Pro stažení instalačního programu se používá HTTP. |
| *. powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Pokročilé řízení front zpráv (AMQP) protokolu |
| *.servicebus.windows.net |443, 9350-9354 |Naslouchací procesy u služby Service Bus Relay přes TCP (vyžaduje port 443 pro získání tokenu služby Access Control) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Použít k testování připojení k Internetu, pokud brána není dostupná ve službě Power BI. |
| *.microsoftonline-p.com |443 |Slouží k ověření v závislosti na konfiguraci. |

### <a name="force-https"></a>Vynucení komunikaci přes protokol HTTPS s Azure Service Bus
Můžete přinutit bránu komunikovat se službou Azure Service Bus pomocí protokolu HTTPS místo přímé TCP; však to uděláte tak může výrazně snížit výkon. Můžete upravit *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* souboru tak, že změníte hodnotu z `AutoDetect` k `Https`. Tento soubor se obvykle nachází ve *C:\Program Files\On-premises data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Správa na úrovni tenantů 

Aktuálně neexistuje žádné jedno místo, kde můžou správci tenantů spravovat všechny brány, které nainstalovali a nakonfigurovali ostatní uživatelé.  Pokud jste správcem tenanta, doporučujeme že požádat uživatele ve vaší organizaci, aby vás přidal jako správce každé brány, kterou nainstalují. To vám umožní spravovat všechny brány ve vaší organizaci prostřednictvím stránky nastavení brány nebo prostřednictvím [příkazy prostředí PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

**Q**: Potřebuji bránu pro zdroje dat v cloudu, jako je Azure SQL Database? <br/>
**A**: Ne. Brána je nezbytná pro připojení k místním zdrojům dat pouze.

**Q**: Brána musí být nainstalovaný na stejném počítači jako zdroj dat? <br/>
**A**: Ne. Brána musí pouze připojení k serveru, obvykle ve stejné síti.

<a name="why-azure-work-school-account"></a>

**Q**: Proč je nutné použít pracovní nebo školní účet pro přihlášení? <br/>
**A**: můžete použít pouze organizace pracovní nebo školní účet při instalaci na místní bránu dat. A že účet musí být ve stejném tenantovi, jako je předplatné můžete prostředku brány v konfiguraci. Váš účet přihlášení je uložený v tenantovi, který je spravovaný službou Azure Active Directory (Azure AD). Obvykle váš účet Azure AD hlavní název uživatele (UPN) odpovídá e-mailovou adresu.

**Q**: kde jsou uloženy Moje přihlašovací údaje? <br/>
**A**: přihlašovací údaje, které zadáte pro zdroj dat jsou zašifrované a uložené v cloudové službě brány. Přihlašovací údaje se dešifrují na místní data brány.

**Q**: existují nějaké požadavky na šířku pásma sítě? <br/>
**A**: je doporučeno má dobrou propustnost připojení k síti. Každé prostředí je jiné a množství dat odesílaných ovlivní výsledky. Pomocí ExpressRoute může pomoct zajistit úroveň propustnosti mezi místními a datovými centry Azure.
Vyhodnocení této propustnosti můžete použít aplikaci Azure Speed Test nástroj třetí strany.

**Q**: Jaká je latence pro spouštění dotazů na zdroj dat z brány? Co je nejvhodnější architektura? <br/>
**A**: Pokud chcete snížit latenci sítě, nainstalujte bránu co nejblíže zdroji dat co nejvíce. Pokud bránu můžete nainstalovat na skutečný zdroj dat, toto blízkosti minimalizuje uvedenou latenci. Vezměte v úvahu datových centrech moc. Například pokud vaše služba používá datové centrum západ USA a SQL Server je hostovaný na Virtuálním počítači Azure, virtuálního počítače Azure by měl být v oblasti západní USA příliš. Tato blízkosti minimalizuje latenci a vyhnete poplatky za výchozí přenos dat na virtuálním počítači Azure.

**Q**: jak se výsledky odesílají zpět do cloudu? <br/>
**A**: výsledky se posílají přes Azure Service Bus.

**Q**: existují nějaká příchozí připojení k bráně z cloudu? <br/>
**A**: Ne. Brána používá odchozí připojení ke službě Azure Service Bus.

**Q**: Co když blokuji odchozí připojení? Co je potřeba otevřít? <br/>
**A**: Podívejte se, portů a hostitelů, které brána používá.

**Q**: co je aktuální služby Windows volat?<br/>
**A**: V služeb se brána nazývá služby On-premises data gateway.

**Q**: Služba Windows brány můžete spustit pomocí účtu služby Azure Active Directory? <br/>
**A**: Ne. Služba Windows musí mít platný účet Windows. Ve výchozím nastavení je služba spuštěna s SID služby NT SERVICE\PBIEgwService.

**Q**: jak na to převzetí bránu? <br/>
**A**: K převzetí brány (spuštěním instalační program nebo změnit v Ovládacích panelech > programy), musíte být vlastníkem prostředku brány v Azure a mají obnovovací klíč. Vlastníci prostředků brány se dají konfigurovat řízení přístupu.

### <a name="high-availability"></a>Vysoká dostupnost a zotavení po havárii

**Q**: jaké možnosti jsou dostupné pro zotavení po havárii? <br/>
**A**: obnovovací klíč můžete použít k obnovení nebo přesunutí brány. Při instalaci brány zadejte obnovovací klíč.

**Q**: co je výhodou obnovovacího klíče? <br/>
**A**: obnovovací klíč poskytuje způsob, jak migrovat nebo obnovit nastavení brány po havárii.

## <a name="troubleshooting"> </a>Řešení potíží

**Q**: Proč nevidím moje brány v seznam instancí brány při pokusu o vytvoření prostředku brány v Azure? <br/>
**A**: existují dva možné důvody. Nejprve je že již vytvořen prostředek brány v aktuální nebo některé jiné předplatné. Chcete-li odstranit tuto možnost, vytvořit výčet prostředků typu **On-premises Data Gateway** z portálu. Je nutné vybrat všechna předplatná, při vytváření výčtu všechny prostředky. Po vytvoření prostředku brány se nezobrazí v seznamu instance brány v prostředí portálu vytvořit prostředek brány. Druhá možnost je jiný než uživatel přihlášený k portálu Azure portal Azure AD identity uživatele, který instalaci brány. Pokud chcete vyřešit, přihlaste se k portálu přes stejný účet jako uživatel, který instalaci brány.

**Q**: Jak můžu zjistit, jaké dotazy se posílají do zdroje dat v místním? <br/>
**A**: můžete povolit trasování dotazů, které obsahuje dotazy, které se odesílají. Nezapomeňte změnit dotaz trasování zpět na původní hodnotu po dokončení odstraňování potíží. Trasování dotazů zapnuté vytvoří větší protokoly.

Můžete se také podívat na nástroje, které váš zdroj dat používá pro trasování dotazů. Například můžete použít Extended Events nebo SQL Profiler pro SQL Server a služby Analysis Services.

**Q**: kde se nacházejí protokoly brány? <br/>
**A**: viz protokoly dále v tomto článku.

### <a name="update"></a>Aktualizovat na nejnovější verzi

Řadě problémů může docházet, když se stane verze brány zastaralá. Jako obecné vhodné Ujistěte se, že používáte nejnovější verzi. Pokud bránu neaktualizovali měsíc nebo i delší dobu, může být zvažte instalaci nejnovější verzi brány a zobrazit, pokud je problém možné reprodukovat.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Chyba: Nepovedlo se přidat uživatele do skupiny. (-2147463168 PBIEgwService Performance Log Users)

Tato chyba může zobrazit, pokud se pokusíte nainstalovat bránu na řadiči domény, což není podporováno. Ujistěte se, že nasazení brány na počítači, který není řadičem domény.

## <a name="logs"></a>Protokoly

Soubory protokolu jsou důležité prostředků při řešení potíží.

#### <a name="enterprise-gateway-service-logs"></a>Protokoly služby brány Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Konfigurační protokoly

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Protokoly událostí

Můžete najít protokoly brány správy dat a PowerBIGateway pod **protokoly aplikací a služeb**.


## <a name="telemetry"></a>Telemetrie
Telemetrie je možné pro monitorování a řešení potíží. Ve výchozím nastavení

**Zapnout telemetrii**

1.  Kontrola klienta On-premises data gateway adresáře na počítači. Obvykle je **%systemdrive%\Program Files\On-premises data gateway**. Nebo můžete otevřít konzolu služby a zkontrolujte cestu ke spustitelnému souboru: vlastnost On-premises data gateway Service.
2.  V souboru Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config z adresáře klienta. Nastavení SendTelemetry změňte na hodnotu true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Uložte změny a restartujte službu Windows: On-premises data gateway služby.




## <a name="next-steps"></a>Další postup
* [Instalace a konfigurace místní brány dat](analysis-services-gateway-install.md).   
* [Správa služby Analysis Services](analysis-services-manage.md)
* [Získání dat ze služby Azure Analysis Services](analysis-services-connect.md)
