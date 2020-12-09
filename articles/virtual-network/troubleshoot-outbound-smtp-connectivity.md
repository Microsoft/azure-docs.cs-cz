---
title: Řešení potíží s odchozím připojením SMTP v Azure | Microsoft Docs
description: Přečtěte si doporučenou metodu pro posílání e-mailů a řešení potíží s odchozím připojením SMTP v Azure.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2018
ms.author: genli
ms.openlocfilehash: d2e5996da5a1fe3f5b154d57ee509f25e54e30ac
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96862356"
---
# <a name="troubleshoot-outbound-smtp-connectivity-issues-in-azure"></a>Řešení potíží s odchozím připojením SMTP v Azure

Od 15. listopadu 2017 se odchozí e-mailové zprávy, které se odesílají přímo do externích domén (například outlook.com a gmail.com) z virtuálního počítače, zpřístupňují jenom pro určité typy předplatného v Microsoft Azure. Odchozí připojení SMTP, která používají port TCP 25, byla zablokována. (Port 25 se primárně používá pro neověřené doručování e-mailů.)

Tato změna chování se týká jenom nových předplatných a nových nasazení od 15. listopadu 2017.

## <a name="recommended-method-of-sending-email"></a>Doporučený způsob odesílání e-mailů

Pro posílání e-mailů z virtuálních počítačů Azure nebo z Azure App Services doporučujeme použít ověřené služby SMTP relay (které se obvykle připojují prostřednictvím portu TCP 587 nebo 443, ale podporují jiné porty). Tyto služby se používají k údržbě IP nebo pověsti domény, aby se minimalizovala možnost, kterou poskytovatelé e-mailů od jiných výrobců odmítnou zprávu. Takové služby SMTP relay zahrnují, ale nejsou omezené na [SendGrid](https://sendgrid.com/partners/azure/). Je také možné, že máte zabezpečenou službu SMTP relay, která běží místně, kterou můžete použít.

Použití těchto služeb doručování e-mailů není v Azure omezené bez ohledu na typ předplatného.

## <a name="enterprise-agreement"></a>Smlouva Enterprise

U smlouva Enterprise uživatelů Azure se nemění technická schopnost posílat e-maily bez použití ověřeného přenosu. Noví i stávající smlouva Enterprise uživatelé můžou zkusit odchozí e-mailové doručování z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu bez jakýchkoli omezení platformy Azure. I když není zaručeno, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele, nebudou pro virtuální počítače v rámci předplatných smlouva Enterprise zablokovány žádné pokusy o doručení. Budete muset pracovat přímo s poskytovateli e-mailu, aby opravili problémy s doručováním zpráv nebo filtrování SPAMu, které zahrnují konkrétní poskytovatele.

## <a name="pay-as-you-go"></a>Průběžné platby

Pokud jste se zaregistrovali do 15. listopadu 2017 pro předplatné s průběžnými platbami, nebudete mít žádnou změnu na technickou schopnost vyzkoušet odchozí e-mailové doručení. V rámci těchto předplatných se budete moci pokusit odchozí e-maily z virtuálních počítačů Azure přímo k externím poskytovatelům e-mailů bez jakýchkoli omezení platformy Azure. Nezaručujeme, že poskytovatelé e-mailů budou přijímat příchozí e-maily od kteréhokoli daného uživatele a uživatelé budou muset pracovat přímo s poskytovateli e-mailu, aby opravili problémy s doručováním zpráv nebo filtrováním nevyžádané pošty, které zahrnují konkrétní poskytovatele

V případě předplatných s průběžnými platbami, které byly vytvořeny po 15. listopadu 2017, budou k dispozici technická omezení, která blokují e-mail, který se odesílá přímo z virtuálních počítačů v rámci těchto předplatných. Pokud chcete mít možnost posílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (bez použití ověřeného přenosu SMTP) a máte účet v dobrém umístění s historií plateb, můžete vytvořit žádost o odebrání omezení v části **připojení** v okně **diagnostikovat a vyřešit** pro prostředek Azure Virtual Network v Azure Portal. Pokud je to možné, vaše předplatné se povolí nebo vám obdržíte pokyny k dalším krokům. 

Po vyloučení předplatného s průběžnými platbami a jejich zastavení a spuštění v Azure Portal jsou všechny virtuální počítače v tomto předplatném předány dál. Výjimka platí jenom pro vyžádané předplatné a jenom pro přenosy virtuálních počítačů, které jsou směrované přímo na Internet.

> [!NOTE]
> Společnost Microsoft si vyhrazuje právo tuto výjimku odvolat, pokud se zjistí, že došlo k porušení podmínek služby.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-vistual-studio-and-free-trial"></a>MSDN, Azure Pass, Systém Azure v rámci licenčního programu Open, vzdělávání, Azure pro studenty, Visual Studio a bezplatná zkušební verze

Pokud jste vytvořili MSDN, Azure Pass, Systém Azure v rámci licenčního programu Open, vzdělávání, Azure student, bezplatnou zkušební verzi nebo jakékoli předplatné sady Visual Studio po 15. listopadu 2017, budete mít technická omezení, která blokují e-mail, který se odesílá z virtuálních počítačů v rámci těchto předplatných přímo poskytovatelům e-mailu. Omezení jsou prováděna za účelem zabránění zneužití. Nebudou uděleny žádné žádosti o odebrání tohoto omezení.

Pokud používáte tyto typy předplatného, doporučujeme používat předávací služby SMTP, jak je uvedeno výše v tomto článku, nebo změnit typ předplatného.

## <a name="cloud-service-provider-csp"></a>Poskytovatel cloudových služeb (CSP)

Pokud používáte prostředky Azure prostřednictvím CSP, můžete vytvořit žádost o odebrání omezení v části **připojení** v okně **diagnostikovat a řešit** pro prostředek Virtual Network na webu Azure Portal. Pokud je to možné, vaše předplatné se povolí nebo vám obdržíte pokyny k dalším krokům.

## <a name="microsoft-partner-network-mpn-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network (MPN), BizSpark Plus nebo Azure Sponsorship

V případě Microsoft Partner Network (MPN), BizSpark Plus nebo Azure Sponsorship předplatných vytvořených po 15. listopadu 2017 budou k dispozici technická omezení, která blokují e-mail, který se odesílá přímo z virtuálních počítačů v rámci těchto předplatných. Pokud chcete možnost posílat e-maily z virtuálních počítačů Azure přímo externím poskytovatelům e-mailu (bez použití ověřeného přenosu SMTP), můžete vytvořit žádost otevřením případu podpory pomocí následujícího typu problému: **technické**  >  **Virtual Network**  >  **připojení**  >  **nemůže odesílat e-maily (SMTP/port 25)**. Ujistěte se, že jste přidali podrobnosti o tom, proč musí vaše nasazení odesílat e-maily přímo poskytovatelům pošty namísto použití ověřeného přenosu. Žádosti budou přezkoumány a schváleny na uvážení společnosti Microsoft. Žádosti mohou být uděleny až po dokončení dalších kontrol ochrany proti podvodům. 

Po vyloučení předplatného a zastavení a spuštění virtuálních počítačů v Azure Portal jsou všechny virtuální počítače v tomto předplatném předány dál. Výjimka platí jenom pro vyžádané předplatné a jenom pro přenosy virtuálních počítačů, které jsou směrované přímo na Internet.

## <a name="restrictions-and-limitations"></a>Omezení a omezení

- Směrování portu 25 přenosů prostřednictvím služeb Azure PaaS, jako je [Azure firewall](https://azure.microsoft.com/services/azure-firewall/) , se nepodporuje.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, obraťte se na [podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , abyste mohli rychle vyřešit problém pomocí následujícího typu problému: **technické**  >  **Virtual Network**  >  **připojení**  >  **nemůže odesílat e-maily (SMTP/port 25)**.
