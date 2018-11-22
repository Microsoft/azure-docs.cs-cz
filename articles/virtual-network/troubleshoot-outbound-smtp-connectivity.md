---
title: Řešení potíží s odchozí připojení SMTP v Azure | Dokumentace Microsoftu
description: Informace o řešení potíží s odchozí připojení SMTP v Azure.
services: virtual-network
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: 1bcdac43eea41a044e920c14310a9f3cd4df793b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285440"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Řešení potíží s odchozí SMTP připojení v Azure

Od 15. listopadu 2017, odchozích e-mailové zprávy, které se odesílají přímo k externím doménám (jako je outlook.com a gmail.com) z virtuálního počítače (VM) jsou k dispozici pouze na určité typy předplatného v Microsoft Azure. Odchozí připojení SMTP, které používají TCP přes port 25 se zablokoval. (Port 25 se používá především pro doručování neověřeného připojení e-mailů.)

Tuto změnu v chování platí pouze pro nová předplatná a nová nasazení od 15. listopadu 2017.

## <a name="recommended-method-of-sending-email"></a>Doporučujeme odeslat e-mailu
Doporučujeme že použít předávací služby ověřený protokol SMTP, (, které obvykle připojení přes port TCP 587 nebo 443, ale podporují jiné porty příliš) k odesílání e-mailů z virtuálních počítačů Azure nebo ze služby Azure App Services. Tyto služby umožňují udržovat reputaci IP adresy nebo domény, aby se minimalizovala možnost poskytovateli e-mailu třetích stran, bude taková zpráva. Takové přenosové služby SMTP patří, ale nejsou omezené na [SendGrid](http://sendgrid.com/partners/azure/). Je také možné, že máte zabezpečené službu předávání přes SMTP, který je spuštěné místně, které můžete použít.

Pomocí těchto služeb doručování e-mailu není omezeno v Azure, bez ohledu na typ předplatného.

## <a name="enterprise-agreement"></a>Smlouva Enterprise
Pro uživatele Azure pro smlouvu Enterprise se nezměnila technické schopnost posílání e-mailu bez použití ověřeného relay. Doručení odchozích e-mailů z virtuálních počítačů Azure přímo poskytovatelům externího e-mailu bez jakýchkoli omezení platformy Azure můžete vyzkoušet nové i stávající uživatelé smlouvy Enterprise. I když není zaručeno, že poskytovateli e-mailu bude přijímat příchozí e-mailů z jakékoli daného uživatele, pokusy o doručení nebude blokovat platformy Azure pro virtuální počítače v rámci předplatných pro smlouvy Enterprise. Budete muset pracovat přímo s poskytovateli e-mailu a opravte jakékoli doručování zpráv nebo problémy, které se týkají konkrétní poskytovatele s filtrováním nevyžádané pošty.

## <a name="pay-as-you-go"></a>Průběžné platby
Pokud jste zaregistrovali před 15. listopadu 2017 s průběžnými platbami nebo předplatné Microsoft Partner Network nabízí, nebude žádná změna v technických možnost vyzkoušet doručení odchozích e-mailů. Můžete i nadále mít možnost vyzkoušet odchozí doručování e-mailů z virtuálních počítačů Azure v rámci těchto předplatných přímo poskytovatelům externího e-mailu bez jakýchkoli omezení platformy Azure. Znovu není zaručeno, že přijetí příchozích e-mailů od libovolného daného uživatele poskytovateli e-mailu a uživatelé budou muset pracovat přímo s poskytovateli e-mailu a opravte jakékoli doručování zpráv nebo problémy, které se týkají konkrétní poskytovatele s filtrováním nevyžádané pošty.

Pro předplatné s průběžnými platbami nebo Microsoft Partner Network, které byly vytvořeny po 15. listopadu 2017 budou mít technická omezení, které blokovat e-mailu, která je odeslána přímo z virtuálních počítačů v rámci těchto předplatných. Pokud chcete mít možnost odesílat e-maily přímo poskytovatelům externího e-mailu (bez použití ověřeného serveru SMTP) z virtuálních počítačů Azure, můžete vytvořit žádost o odebrání tohoto omezení. Požadavky, bude zkontrolován a schválen na základě vlastního uvážení Microsoftu a budete mít udělena pouze po provedení dalších kontrol v souvislosti s možnými podvody. Pokud chcete vytvořit žádost, otevřete případ podpory s použitím následující typ problému: **technické** > **virtuální sítě** > **připojení**  >  **Nemůžou odesílat e-maily (SMTP/Port 25)**. Ujistěte se, že přidáte podrobnosti, proč k odesílání e-mailu přímo poskytovatelům e-mailu místo použití ověřeného relay má vaše nasazení.

Když předplatné s průběžnými platbami nebo Microsoft Partner Network virtuálních počítačů v rámci tohoto předplatného pouze se má vyloučit do budoucna.

> [!NOTE]
> Společnost Microsoft si vyhrazuje právo odebrat tato výjimka, pokud je zjištěno, že došlo k porušení podmínek služby.

## <a name="msdn-azure-pass-azure-in-open-education-bizspark-and-free-trial"></a>MSDN, Azure Pass, Azure v programu Open, vzdělávání, BizSpark a bezplatná zkušební verze
Pokud jste vytvořili webu MSDN, Azure Pass, Azure v Open, vzdělávání, BizSpark a bezplatná zkušební verze předplatného po 15. listopadu 2017, budete mít technická omezení bloku e-mail odeslaný z virtuálních počítačů v rámci těchto předplatných přímo k e-mailu poskytovatelů. Zamezilo se provádějí omezení. Udělí se žádné žádosti o odebrání tohoto omezení.

Pokud používáte tyto typy předplatného, už vám doporučujeme využívat služby pro přenos přes SMTP, jak je uvedeno výše v tomto článku.

## <a name="cloud-service-provider-csp"></a>Poskytovatele cloudových služeb (CSP)

Pokud používáte prostředky Azure prostřednictvím zprostředkovatele kryptografických služeb, budete moct vytvořit lístek podpory prostřednictvím podle vašeho výběru zprostředkovatele kryptografických služeb a můžete požádat o poskytovateli CSP vaším jménem vytvářet s případem odblokovat, pokud se nedá použít zabezpečeného serveru SMTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
