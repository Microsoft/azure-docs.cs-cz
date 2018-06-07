---
title: Místní data brána | Microsoft Docs
description: Bránu místní je nezbytný, pokud váš server služby Analysis Services v Azure se připojí k místní datové zdroje.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 60a04d492798da8292e2c9d4107e21e9039f7d40
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34596851"
---
# <a name="connecting-to-on-premises-data-sources-with-azure-on-premises-data-gateway"></a>Připojení k místní zdroje dat s Azure místní brány dat
Místní data brána funguje jako mostu, zajištění zabezpečených dat přenos mezi místní zdroje dat a vaše servery Azure Analysis Services v cloudu. Kromě práce s více servery Azure Analysis Services ve stejné oblasti, nejnovější verzi brány taky spolupracuje se službou Azure Logic Apps, Power BI, Power aplikace a Flow společnosti Microsoft. S jednou bránou můžete přidružit více služeb ve stejné oblasti. 

Získání Instalační program s bránou prvním je proces, který sestávající ze čtyř částí:

- **Stáhněte a spusťte instalační program** – tento krok nainstaluje služba brány do počítače ve vaší organizaci. Také přihlášení do Azure pomocí účtu ve vaší [klienta](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) Azure AD. Azure B2B (Host) účty nejsou podporované.

- **Zaregistrujte bránu** – v tomto kroku, zadejte název a klíč pro bránu pro obnovení a vyberte oblast, registrace brány cloudové službě brány. V libovolné oblasti se dají registrovat prostředku brány, ale doporučujeme, aby že byl ve stejné oblasti jako vaše servery služby Analysis Services. 

- **Vytvořte prostředek brány v Azure** – v tomto kroku vytvoříte bránu prostředků ve vašem předplatném Azure.

- **Připojte své servery k prostředku brány** – až budete mít bránu prostředků v rámci vašeho předplatného, můžete začít vaše servery k němu připojuje. K nim mohla připojit víc serverů a dalším prostředkům.

Pokud chcete začít hned, najdete v části [nainstalujte a nakonfigurujte místní brána dat](analysis-services-gateway-install.md).

## <a name="how-it-works"> </a>Jak to funguje
Brána je nainstalujte do počítače ve vaší organizaci používá jako služby systému Windows, **místní brána dat**. Tato místní služba není zaregistrována cloudové službě brány přes Azure Service Bus. Pak vytvořte prostředek brány cloudové službě brány pro vaše předplatné Azure. Vaše servery Azure Analysis Services jsou připojena k vaší brány prostředku. Při modely na serveru je nutné se připojit k místním datům zdroje pro dotazy nebo zpracování, prochází přes prostředek brány, Azure Service Bus, místní služba brány dat a zdroje dat dotazu a datový tok. 

![Jak to funguje](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Tok dotazy a data:

1. Dotaz byl vytvořený Cloudová služba se zašifrované přihlašovací údaje pro zdroj dat na místě. Pak se odešlou do fronty pro bránu ke zpracování.
2. Cloudová služba brány analyzuje dotaz a nabízených oznámení požadavku [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).
3. Bránu dat místní dotáže Azure Service Bus pro žádosti čekající na vyřízení.
4. Brána získá dotaz, dešifruje přihlašovací údaje a připojuje ke zdroji dat pomocí těchto přihlašovacích údajů.
5. Brána odešle dotaz na zdroj dat pro spuštění.
6. Výsledky jsou odesílány ze zdroje dat zpět na bránu a pak do cloudové služby a vaším serverem.

## <a name="windows-service-account"> </a>Účet služby systému Windows
Místní brána dat je nakonfigurován pro použití *NT SERVICE\PBIEgwService* pro přihlašovací pověření služby systému Windows. Ve výchozím nastavení má právo přihlášení jako služba; v kontextu na počítač, který instalujete na bránu. Tento přihlašovací údaj není stejný účet používaný pro připojení k místním zdrojům dat nebo účtu Azure.  

Pokud narazíte na potíže s proxy serveru z důvodu ověřování, můžete změnit účet služby systému Windows na uživatele domény nebo účet spravované služby.

## <a name="ports"> </a>Porty
Brána vytvoří odchozí připojení k Azure Service Bus. Komunikuje na odchozí porty: TCP 443 (výchozí), 5671, 5672, 9350 prostřednictvím 9354.  Brána nevyžaduje příchozí porty.

Doporučujeme povolených IP adres pro vaši oblast dat v bráně firewall. Si můžete stáhnout [seznamu Microsoft Azure Datacenter IP](https://www.microsoft.com/download/details.aspx?id=41653). Tento seznam je aktualizovaný týdně.

> [!NOTE]
> V notaci CIDR, jsou uvedené v seznamu IP Datacentra Azure IP adresy. Další informace najdete v tématu [směrování mezi doménami](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).
>
>

Níže jsou uvedeny plně kvalifikované názvy domény používá bránu.

| Názvy domén | Odchozí porty | Popis |
| --- | --- | --- |
| *. powerbi.com |80 |HTTP používaný ke stahování instalační služby. |
| *. powerbi.com |443 |HTTPS |
| *.analysis.windows.net |443 |HTTPS |
| *.login.windows.net |443 |HTTPS |
| *.servicebus.windows.net |5671-5672 |Pokročilé zpráv služby Řízení front Protocol (AMQP) |
| *.servicebus.windows.net |443, 9350-9354 |Moduly pro naslouchání na předávání přes Service Bus přes TCP (vyžaduje 443 pro získání tokenu řízení přístupu) |
| *.frontend.clouddatahub.net |443 |HTTPS |
| *.core.windows.net |443 |HTTPS |
| login.microsoftonline.com |443 |HTTPS |
| *.msftncsi.com |443 |Použít k testování připojení k Internetu, pokud brána nedostupná pro službu Power BI. |
| *.microsoftonline-p.com |443 |Slouží k ověření v závislosti na konfiguraci. |

### <a name="force-https"></a>Vynucení komunikaci přes protokol HTTPS s Azure Service Bus
Můžete vynutit bránu a komunikovat s Azure Service Bus pomocí protokolu HTTPS místo přímé TCP; ale to tak může výrazně snížit výkon. Můžete upravit *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* souboru změnou hodnoty z `AutoDetect` k `Https`. Tento soubor se obvykle nachází ve *brána dat Files\On místní C:\Program*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```

## <a name="tenant-level-administration"></a>Úroveň správy klienta 

Aktuálně neexistuje žádné jediné místo, kde správci klientů můžou spravovat všechny brány, které jiní uživatelé nainstalován a nakonfigurován.  Pokud jste správce klienta, doporučuje že se požádat uživatele ve vaší organizaci můžete přidat jako správce pro všechny brány, který se má nainstalovat. To vám umožňuje spravovat všechny brány ve vaší organizaci prostřednictvím stránky nastavení brány, nebo přes [příkazy prostředí PowerShell](https://docs.microsoft.com/power-bi/service-gateway-high-availability-clusters#powershell-support-for-gateway-clusters). 


## <a name="faq"></a>Nejčastější dotazy

### <a name="general"></a>Obecné

**Q**: Potřebuji bránu pro zdroje dat v cloudu, jako je například Azure SQL Database? <br/>
**A**: Ne. Brána je nezbytné pro připojení k místním zdrojům dat pouze.

**Q**: nemá brány musí být instalovány na stejném počítači jako zdroj dat? <br/>
**A**: Ne. Brány musí pouze možnost připojení k serveru, obvykle ve stejné síti.

<a name="why-azure-work-school-account"></a>

**Q**: Proč je nutné použít pracovní nebo školní účet pro přihlášení? <br/>
**A**: můžete použít pouze organizace pracovní nebo školní účet při instalaci bránu dat na místě. A že účet musí být ve stejném klientovi jako předplatné můžete konfigurujete prostředek brány. Váš přihlašovací účet je uložený v klientovi, který je spravován pomocí služby Azure Active Directory (Azure AD). Obvykle účet Azure AD hlavní název uživatele (UPN) odpovídá e-mailovou adresu.

**Q**: uložení pověření? <br/>
**A**: přihlašovací údaje, které zadáte pro zdroj dat jsou zašifrovány a uložené v cloudové službě brány. Přihlašovací údaje jsou v bráně místní data dešifrovat.

**Q**: existují všechny požadavky pro šířku pásma sítě? <br/>
**A**: doporučujeme připojení k síti má dobrou propustnost. Každé prostředí je jiné a množství dat odesílaných ovlivňuje výsledky. Pomocí ExpressRoute může pomoct zajistit úrovní propustnosti mezi místními a datových centrech Azure.
Můžete aplikaci Azure rychlost testovací nástroj třetí strany ke měřidla vaší propustnost.

**Q**: co je latence spouštět dotazy na zdroj dat z brány? Co je nejlepší architektura? <br/>
**A**: Chcete-li snížit latenci sítě, nainstalujte bránu co nejblíže zdroji dat jako možné. Pokud nainstalujete brány na skutečná data zdroj tento blízkosti minimalizuje latenci zavedená. Příliš zvažte v datacentrech. Například pokud používá službu západní USA datacenter a vy musíte SQL Server hostované ve virtuálním počítači Azure, svého virtuálního počítače Azure musí být v západní USA příliš. Tato blízkosti minimalizuje latenci a zabraňuje s nimi spojeným nákladům na virtuálním počítači Azure.

**Q**: jak se výsledky odesílají zpět do cloudu? <br/>
**A**: výsledky se odesílají přes Azure Service Bus.

**Q**: existují všechna příchozí připojení k bráně z cloudu? <br/>
**A**: Ne. Brána používá odchozí připojení k Azure Service Bus.

**Q**: Co když blokovat odchozí připojení? Co je potřeba otevřít? <br/>
**A**: najdete v části Nastavení portů a hostitelů, které používá bránu.

**Q**: co je aktuální služby Windows volána?<br/>
**A**: V služby brány se nazývá služba brány pro místní data.

**Q**: můžete bránu služby systému Windows spustit pomocí účtu Azure Active Directory? <br/>
**A**: Ne. Služba systému Windows musí mít platný účet systému Windows. Ve výchozím nastavení spouští službu s SID služby, NT SERVICE\PBIEgwService.

**Q**: jak na to převzetí bránu? <br/>
**A**: K převzetí brány (tak, že spustíte instalační program nebo změnit v Ovládacích panelech > programy), musíte být vlastníkem prostředku brány v Azure a mít obnovovací klíč. Vlastníci prostředků brány lze konfigurovat v řízení přístupu.

### <a name="high-availability"></a>Vysoká dostupnost a zotavení po havárii

**Q**: jaké možnosti jsou dostupné pro zotavení po havárii? <br/>
**A**: obnovovací klíč můžete použít k obnovení nebo přesunout bránu. Při instalaci brány, zadejte obnovovací klíč.

**Q**: co je výhodou obnovovací klíč? <br/>
**A**: obnovovací klíč poskytuje způsob, jak migrovat nebo obnovení po havárii nastavení brány.

## <a name="troubleshooting"> </a>Řešení potíží

**Q**: Proč nevidím moje brány v seznamu instancí brány při pokusu o vytvoření prostředku brány v Azure? <br/>
**A**: existují dvě možné příčiny. Nejprve je že již vytvořen prostředek pro bránu v aktuální nebo některé jiné předplatné. Pokud chcete odstranit tuto možnost, provést výčet prostředků typu **místní brány Data Gateways** z portálu. Ujistěte se, že při vytváření výčtu všechny prostředky, vyberte všechny odběry. Po vytvoření prostředku brány se nezobrazí v seznamu instancí brány v prostředí portálu vytvořit prostředek brány. Druhá možnost je, že Azure AD identity uživatele, který bránu nainstalovali, se liší od uživateli přihlášení k portálu Azure. Chcete-li vyřešit, přihlaste se k portálu pomocí stejného účtu jako uživatel, který bránu nainstalovali.

**Q**: jak můžete zjistit, co jsou dotazy se posílá místní zdroj dat? <br/>
**A**: můžete povolit trasování dotazů, který obsahuje dotazy, které se odesílají. Nezapomeňte změnit dotaz trasování zpět na původní hodnotu po dokončení odstraňování potíží. Trasování dotazů, které jsou zapnuté vytvoří větší protokoly.

Můžete také zobrazit nástroje, které má váš zdroj dat pro trasování dotazů. Můžete například použít rozšířených událostí nebo profileru SQL pro SQL Server a služby Analysis Services.

**Q**: kde jsou protokoly gateway? <br/>
**A**: viz protokoly později v tomto článku.

### <a name="update"></a>Aktualizovat na nejnovější verzi

Mnohé problémy můžete surface, když se stane zastaralé verze brány. Jako vhodné obecné Ujistěte se, že používáte nejnovější verzi. Pokud jste neprovedli aktualizaci brány pro měsíc i déle, můžete zvažte instalaci nejnovější verze brány a v tématu, pokud jste reprodukujte problém.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users"></a>Chyba: Nepodařilo se přidat uživatele do skupiny. (-2147463168 PBIEgwService výkonu protokolu uživatelů)

Může se tato chyba, pokud se pokusíte nainstalovat bránu na řadič domény, což není podporováno. Ujistěte se, která můžete nasadit bránu na počítači, který není řadičem domény.

## <a name="logs"></a>Protokoly

Soubory protokolu jsou důležité prostředků při řešení potíží.

#### <a name="enterprise-gateway-service-logs"></a>Protokoly služby Enterprise gateway

`C:\Users\PBIEgwService\AppData\Local\Microsoft\On-premises data gateway\<yyyyymmdd>.<Number>.log`

#### <a name="configuration-logs"></a>Konfigurace protokolů

`C:\Users\<username>\AppData\Local\Microsoft\On-premises data gateway\GatewayConfigurator.log`




#### <a name="event-logs"></a>Protokoly událostí

Můžete najít v protokolech Brána pro správu dat a PowerBIGateway pod **protokoly aplikací a služeb**.


## <a name="telemetry"></a>Telemetrie
Telemetrická data lze použít pro monitorování a řešení potíží. Ve výchozím nastavení

**Chcete-li zapnout telemetrii**

1.  Zkontrolujte místní adresář klienta brány dat v počítači. Obvykle je **%systemdrive%\Program Files\On místní brána dat**. Nebo můžete spustit konzolu služby a zkontrolujte cestu ke spustitelnému souboru: vlastnost službu brány místní data.
2.  V souboru Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config z adresáře klienta. Nastavení SendTelemetry změňte na hodnotu true.
        
    ```
        <setting name="SendTelemetry" serializeAs="String">
                    <value>true</value>
        </setting>
    ```

3.  Uložte změny a restartovat službu systému Windows: místní služba brány pro data.




## <a name="next-steps"></a>Další postup
* [Instalace a konfigurace místní brána dat](analysis-services-gateway-install.md).   
* [Správa služby Analysis Services](analysis-services-manage.md)
* [Získání dat z Azure Analysis Services](analysis-services-connect.md)
