---
title: Povolení koncového šifrování protokolu SSL ve službě Azure Application Gateway
description: Tento článek představuje přehled služby Application Gateway podporovat kompletního protokolu SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 10/23/2018
ms.author: amsriva
ms.openlocfilehash: 626db07a81c6482a689329b8cddc9f40b464bb7e
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649070"
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Přehled koncového šifrování protokolu SSL pomocí služby Application Gateway

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-end serverů. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Pro některé zákazníky je však nešifrovaná komunikace s back-endovými servery nepřijatelnou možností. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace služba Application Gateway podporuje koncové šifrování protokolu SSL.

Koncové šifrování protokolu SSL umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované, zatímco stále využívat výhody funkcí vyrovnávání zatížení vrstvy 7 které služba application gateway poskytuje. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Když je nakonfigurována s režimem komunikace koncového šifrování protokolu SSL, služba Application Gateway ukončuje na bráně relace protokolu SSL a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Služba Application Gateway poté zahájí nové připojení SSL k back-endovému serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu s veřejným klíčem back-endového serveru. Koncové šifrování protokolu SSL se povoluje nastavením nastavení protokolu **nastavení BackendHTTPSetting** na protokol HTTPS, který se následně použije na back-endový fond. Každý back-endový server v back-endovém fondu s povoleným koncovým šifrováním protokolu SSL musí být pro umožnění bezpečné komunikace nakonfigurován s certifikátem.

![scénář koncového šifrování protokolu ssl][1]

V tomto příkladu jsou požadavky, které používají šifrování TLS 1.2, přesměrované koncovým šifrováním protokolu SSL na back-endové servery fondu Pool1.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Koncové šifrování protokolu SSL a vytváření seznamu povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být povolené ve službě application gateway popsané v předchozích krocích předtím, než je možné.

> [!NOTE]
> Instalace certifikátu ověřování se nevyžaduje pro důvěryhodného služby Azure, jako je Azure Web Apps.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Koncové šifrování protokolu SSL s v2 SKU

Ověřovací certifikáty byly zastaralé a nahrazují důvěryhodných kořenových certifikátů ve službě Application Gateway SKU v2. Fungují podobně jako ověřovací certifikáty s několik klíčových rozdílů:

- Certifikáty podepsané dobře známé Certifikační autority jehož CN odpovídá názvu hostitele v nastavení HTTP back-endu nevyžadují žádné další krok pro kompletního protokolu SSL pro práci. 

   Například pokud společnost dobře známé certifikační Autorita vydá nové certifikáty back-endu a má CN contoso.com a pole nastavení http back-endu hostitele je také nastavena na contoso.com, pak nejsou žádné další kroky požadované. Můžete nastavit back-endu http nastavení protokolu HTTPS a oba stavu sondy cesta bude protokol SSL povolený. Pokud používáte Azure Web Apps nebo jiné služby Azure web jako back-endu, ty jsou také implicitně důvěryhodné a žádné další kroky jsou požadovány pro kompletního protokolu SSL.
- Pokud certifikát podepsaný svým držitelem nebo podepsány Neznámý prostředníci, pak k povolení koncového šifrování protokolu SSL v SKU v2 pro důvěryhodného kořenového certifikátu musí být definovaný. Aplikační brána bude komunikovat jenom s back-EndY certifikát serveru, jehož kořenový certifikát odpovídá jednomu z seznam důvěryhodných kořenových certifikátů v nastavení http back-endu přidružené k fondu.
- Kromě shodu kořenový certifikát služba Application Gateway také ověří, jestli se hostitel nastavení zadaného v nastavení http back-end se shoduje s běžného názvu (CN) předložený certifikát SSL back-endového serveru. Při pokusu o navázání připojení protokolem SSL k back-endu, nastaví služba Application Gateway rozšíření indikace názvu serveru (SNI) hostitel zadaný v nastavení http back-endu.
- Pokud **vybrat název hostitele z back-endová adresa** je vybrán místo pole Host v nastavení http back-end, pak záhlaví SNI je vždycky nastavený na back-endový fond, plně kvalifikovaný název domény a CN na back-end serveru SSL certifikát se musí shodovat jeho plně kvalifikovaný název domény. V tomto scénáři nejsou podporovány členů fondu back-end s IP adresami.
- Kořenový certifikát je ve formátu base64 kódovaného kořenový certifikát z certifikátů back-endového serveru.

## <a name="next-steps"></a>Další postup

Po získání informací o koncovém šifrování protokolu SSL přejděte k tématu [Konfigurace aplikační brány s ukončením protokolu SSL pomocí webu Azure Portal](create-ssl-portal.md) a vytvořte aplikační bránu s koncovým šifrováním protokolu SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
