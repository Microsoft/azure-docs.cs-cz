---
title: Řešení potíží s diagnostikou připojení v prostředí SSIS Integration runtime
description: Tento článek popisuje pokyny k odstraňování potíží s diagnostikou připojení v prostředí SSIS Integration runtime.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: 8e520048a6067f134e847953f4f4aa0598d9926e
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86172598"
---
# <a name="troubleshoot-diagnose-connectivity-in-the-ssis-integration-runtime"></a>Řešení potíží s diagnostikou připojení v prostředí SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Pokud narazíte na problémy s připojením při spouštění balíčků SSIS v prostředí SSIS Integration runtime, zejména v případě, že se váš modul runtime integrace SSIS připojil ke službě Azure Virtual Network. Můžete zkusit provést samočinnou diagnostiku problémů pomocí této funkce diagnostiky připojení na stránce monitorování SSIS Integration runtime portálu Azure Data Factory. 

 ![Stránka monitorování – Diagnostika ](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png) ![ monitorování připojení stránka-Test připojení](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)
 
Tento článek obsahuje nejběžnější chyby, se kterými se můžete setkat při testování připojení v prostředí SSIS Integration runtime. Popisuje možné příčiny a akce pro řešení chyb. 

## <a name="common-errors-potential-causes-and-recommendation-solutions"></a>Běžné chyby, potenciální příčiny a řešení pro doporučení

### <a name="error-code-invalidinput"></a>Kód chyby: InvalidInput.
* **Chybová zpráva**: Ověřte prosím správnost vstupu.
* **Možné příčiny:** Váš vstup není správný.
* **Doporučení:** Zkontrolujte prosím svůj vstup.

### <a name="error-code-firewallornetworkissue"></a>Kód chyby: FirewallOrNetworkIssue.
* **Chybová zpráva**: Ověřte prosím, jestli je tento port otevřený v bráně firewall/serveru/NSG a síť je stabilní.
* **Možné příčiny:** 
  * Váš server neotevírá tento port.
  * Vaše skupina zabezpečení sítě má na tomto portu odepřený odchozí provoz.
  * Vaše brána firewall síťové virtuální zařízení/Azure/on-Prem firewall neotevírá tento port.
* **Základě** 
  * Otevřete tento port na serveru.
  * Aktualizujte skupinu zabezpečení sítě tak, aby povolovala odchozí přenosy na tomto portu.
  * Otevřete tento port na síťové virtuální zařízení/Azure firewall/on-Prem firewall.

### <a name="error-code-misconfigureddnssettings"></a>Kód chyby: MisconfiguredDnsSettings.
* **Chybová zpráva**: Pokud používáte vlastní server DNS ve virtuální síti připojené pomocí Azure-SSIS IR, ověřte prosím, jestli dokáže přeložit název hostitele.
* **Možné příčiny:** 
  *  Problém vlastního DNS
  *  Pro název privátního hostitele nepoužíváte plně kvalifikovaný název domény (FQDN).
* **Základě** 
  *  Opravte vlastní problém DNS, abyste se ujistili, že dokáže přeložit název hostitele.
  *  Použijte plně kvalifikovaný název domény (FQDN), třeba použít <your_private_server>. contoso.com místo <your_private_server>, protože Azure-SSIS IR automaticky nepřipojí vlastní příponu DNS.

### <a name="error-code-servernotallowremoteconenction"></a>Kód chyby: ServerNotAllowRemoteConenction.
* **Chybová zpráva**: Ověřte prosím, jestli váš server umožňuje přes tento port vzdálená připojení TCP.
* **Možné příčiny:** 
  *  Brána firewall serveru nepovoluje vzdálená připojení TCP.
  *  Váš server není online.
* **Základě** 
  *  Povolte vzdálená připojení TCP v bráně firewall serveru.
  *  Spusťte server.
   
### <a name="error-code-misconfigurednsgsettings"></a>Kód chyby: MisconfiguredNsgSettings.
* **Chybová zpráva**: Ověřte prosím, že NSG vaší virtuální sítě umožňuje odchozí přenosy přes tento port. Pokud používáte Azure ExpressRoute a nebo UDR, ověřte prosím, jestli je tento port v bráně firewall nebo na serveru otevřený.
* **Možné příčiny:** 
  *  Vaše skupina zabezpečení sítě má na tomto portu odepřený odchozí provoz.
  *  Vaše brána firewall síťové virtuální zařízení/Azure/on-Prem firewall neotevírá tento port.
* **Základě** 
  *  Aktualizujte skupinu zabezpečení sítě tak, aby povolovala odchozí přenosy na tomto portu.
  *  Otevřete tento port na síťové virtuální zařízení/Azure firewall/on-Prem firewall.

### <a name="error-code-genericissues"></a>Kód chyby: GenericIssues.
* **Chybová zpráva**: test připojení se nezdařil z důvodu obecných problémů.
* **Možné příčiny:** Test připojení zjistil obecný dočasný problém.
* **Doporučení:** Zkuste prosím testovací připojení znovu později. Pokud se tento pokus nepomůže, obraťte se prosím na tým podpory Azure Data Factory.


### <a name="error-code-pspingexecutiontimeout"></a>Kód chyby: PSPingExecutionTimeout.
* **Chybová zpráva**: časový limit testovacího připojení, zkuste to prosím znovu později.
* **Možné příčiny:** Vypršel časový limit pro test připojení.
* **Doporučení:** Zkuste prosím testovací připojení znovu později. Pokud se tento pokus nepomůže, obraťte se prosím na tým podpory Azure Data Factory.

### <a name="error-code-networkinstable"></a>Kód chyby: NetworkInstable.
* **Chybová zpráva**: test připojení byl neúspěšný, protože došlo k nestabilitě sítě.
* **Možné příčiny:** Přechodný problém sítě.
* **Doporučení:** Zkontrolujte prosím, jestli je server nebo síť brány firewall stabilní.

## <a name="next-steps"></a>Další kroky

- Nasaďte balíčky. Další informace najdete v tématu [nasazení projektu SSIS do Azure pomocí SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms).
- Spusťte balíčky. Další informace najdete v tématu [spuštění balíčků SSIS v Azure pomocí SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms).
- Naplánujte balíčky. Další informace najdete v tématu [plánování balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15).

