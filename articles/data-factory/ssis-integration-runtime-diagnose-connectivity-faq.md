---
title: Použití funkce diagnostiky připojení v prostředí SSIS Integration runtime
description: Řešení potíží s připojením v prostředí SSIS Integration runtime pomocí funkce diagnostiky připojení.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: meiyl
author: meiyl
ms.reviewer: sawinark
manager: yidetu
ms.date: 06/07/2020
ms.openlocfilehash: cf41da685036770144ebf7eb2befd0c3d126362d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87446027"
---
# <a name="use-the-diagnose-connectivity-feature-in-the-ssis-integration-runtime"></a>Použití funkce diagnostiky připojení v prostředí SSIS Integration runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při provádění balíčků služba SSIS (SQL Server Integration Services) (SSIS) v prostředí SSIS Integration runtime můžete najít problémy s připojením. K těmto potížím dochází hlavně v případě, že SSIS Integration runtime propojuje službu Azure Virtual Network.

Vyřešte problémy s připojením pomocí funkce *diagnostiky připojení* a otestujte připojení. Tato funkce se nachází na stránce monitorování SSIS Integration runtime na portálu Azure Data Factory.

 ![Stránka monitorování – Diagnostika připojení](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-diagnose-connectivity.png)

 ![Monitorovat stránku-testovat připojení](media/ssis-integration-runtime-diagnose-connectivity-faq/ssis-monitor-test-connection.png)

V následujících částech se dozvíte o nejběžnějších chybách, ke kterým dochází při testování připojení. Každá část popisuje:

- Kód chyby
- Chybová zpráva
- Možné příčiny chyby
- Doporučená řešení

## <a name="error-code-invalidinput"></a>Kód chyby: InvalidInput

- **Chybová zpráva**: Ověřte prosím správnost vstupu.
- **Potenciální příčina**: váš vstup není správný.
- **Doporučení**: Ověřte si zadání.

## <a name="error-code-firewallornetworkissue"></a>Kód chyby: FirewallOrNetworkIssue

- **Chybová zpráva**: Ověřte prosím, jestli je tento port otevřený v bráně firewall/serveru/NSG a síť je stabilní.
- **Možné příčiny:**
  - Váš server neotevře port.
  - Vaše skupina zabezpečení sítě má odepřený odchozí provoz na portu.
  - SÍŤOVÉ virtuální zařízení/Azure Firewall/místní brána firewall neotevírá port.
- **Doporučit**
  - Otevřete port na serveru.
  - Aktualizujte skupinu zabezpečení sítě tak, aby povolovala odchozí přenosy na portu.
  - Otevřete port na síťové virtuální zařízení/Azure Firewall/místní bránu firewall.

## <a name="error-code-misconfigureddnssettings"></a>Kód chyby: MisconfiguredDnsSettings

- **Chybová zpráva**: Pokud používáte vlastní server DNS ve virtuální síti, který jste připojili k vašemu Azure-SSIS IR, ověřte, že dokáže přeložit název hostitele. "
- **Možné příčiny:**
  -  Došlo k potížím s vlastním DNS.
  -  Pro název privátního hostitele nepoužíváte plně kvalifikovaný název domény (FQDN).
- **Doporučit**
  -  Opravte vlastní problém DNS, abyste se ujistili, že dokáže přeložit název hostitele.
  -  Použijte plně kvalifikovaný název domény. Azure-SSIS IR automaticky nepřipojí vlastní příponu DNS. Použijte například **<your_private_server>. contoso.com** namísto **<your_private_server>**.

## <a name="error-code-servernotallowremoteconnection"></a>Kód chyby: ServerNotAllowRemoteConnection

- **Chybová zpráva**: "Ověřte prosím, jestli je na serveru povoleno vzdálené připojení TCP prostřednictvím tohoto portu."
- **Možné příčiny:**
  -  Brána firewall serveru nepovoluje vzdálená připojení TCP.
  -  Váš server není online.
- **Doporučit**
  -  Povolte vzdálená připojení TCP v bráně firewall serveru.
  -  Spusťte server.
   
## <a name="error-code-misconfigurednsgsettings"></a>Kód chyby: MisconfiguredNsgSettings

- **Chybová zpráva**: Ověřte prosím, jestli NSG vaší virtuální sítě umožňuje odchozí přenosy přes tento port. Pokud používáte Azure ExpressRoute a nebo UDR, ověřte prosím, jestli je tento port v bráně firewall nebo na serveru otevřený.
- **Možné příčiny:**
  -  Vaše skupina zabezpečení sítě má odepřený odchozí provoz na portu.
  -  SÍŤOVÉ virtuální zařízení/Azure Firewall/místní brána firewall neotevírá port.
- **Základě**
  -  Aktualizujte skupinu zabezpečení sítě tak, aby povolovala odchozí přenosy na portu.
  -  Otevřete port na síťové virtuální zařízení/Azure Firewall/místní bránu firewall.

## <a name="error-code-genericissues"></a>Kód chyby: GenericIssues

- **Chybová zpráva**: "test připojení selhalo z důvodu obecných problémů".
- **Potenciální příčina**: test Connection zjistil obecný dočasný problém.
- **Doporučení**: zkuste připojení otestovat později. Pokud se k opakovanému pokusu nepodaří, obraťte se na tým podpory Azure Data Factory.

## <a name="error-code-pspingexecutiontimeout"></a>Kód chyby: PSPingExecutionTimeout

- **Chybová zpráva**: "časový limit připojení testu", zkuste to prosím znovu později. "
- **Potenciální příčina**: vypršel časový limit připojení testu.
- **Doporučení**: zkuste připojení otestovat později. Pokud se k opakovanému pokusu nepodaří, obraťte se na tým podpory Azure Data Factory.

## <a name="error-code-networkinstable"></a>Kód chyby: NetworkInstable

- **Chybová zpráva**: "test připojení se nezdařil z důvodu nestability sítě".
- **Potenciální příčina**: přechodný problém sítě.
- **Doporučení**: Ověřte, zda je server nebo síť brány firewall stabilní.

## <a name="next-steps"></a>Další kroky

- [Nasazení projektu SSIS do Azure pomocí SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-deploy-ssms)
- [Spouštění balíčků SSIS v Azure s SSMS](https://docs.microsoft.com/sql/integration-services/ssis-quickstart-run-ssms)
- [Plánování balíčků SSIS v Azure](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms?view=sql-server-ver15)
