---
title: Povolení koncového šifrování protokolu SSL ve službě Azure Application Gateway
description: Tento článek představuje přehled služby Application Gateway podporovat kompletního protokolu SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 8/6/2018
ms.author: amsriva
ms.openlocfilehash: 4575bed18697a5661d58dc350c24a9497f7c46ff
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578809"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Přehled koncového šifrování protokolu SSL pomocí služby Application Gateway

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-end serverů. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Pro některé zákazníky je však nešifrovaná komunikace s back-endovými servery nepřijatelnou možností. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace služba Application Gateway podporuje koncové šifrování protokolu SSL.

Koncové šifrování protokolu SSL umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované podobě a současně využívat výhody funkcí pro vyrovnávání zatížení vrstvy 7, které nabízí aplikační brána. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Když je nakonfigurována s režimem komunikace koncového šifrování protokolu SSL, služba Application Gateway ukončuje na bráně relace protokolu SSL a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Služba Application Gateway poté zahájí nové připojení SSL k back-endovému serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu s veřejným klíčem back-endového serveru. Koncové šifrování protokolu SSL se povoluje nastavením nastavení protokolu **nastavení BackendHTTPSetting** na protokol HTTPS, který se následně použije na back-endový fond. Každý back-endový server v back-endovém fondu s povoleným koncovým šifrováním protokolu SSL musí být pro umožnění bezpečné komunikace nakonfigurován s certifikátem.

![scénář koncového šifrování protokolu ssl][1]

V tomto příkladu jsou požadavky, které používají šifrování TLS 1.2, přesměrované koncovým šifrováním protokolu SSL na back-endové servery fondu Pool1.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Koncové šifrování protokolu SSL a vytváření seznamu povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být povolené ve službě application gateway popsané v předchozích krocích předtím, než je možné.

> [!NOTE]
> Instalace certifikátu ověřování se nevyžaduje pro důvěryhodného služby Azure, jako je Azure Web Apps.

## <a name="next-steps"></a>Další postup

Po získání informací o koncovém šifrování protokolu SSL přejděte k tématu [Konfigurace aplikační brány s ukončením protokolu SSL pomocí webu Azure Portal](create-ssl-portal.md) a vytvořte aplikační bránu s koncovým šifrováním protokolu SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
