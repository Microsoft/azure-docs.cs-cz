## <a name="scenario"></a>Scénář
Tento průvodce detailně až po nasazení, který používá statickou veřejnou IP adresu přidělená k virtuálnímu počítači (VM). V tomto scénáři máte jeden virtuální počítač s vlastní statickou veřejnou IP adresu. Virtuální počítač je součástí podsítě s názvem **front-endu** a má také statickou privátní IP adresou (**192.168.1.101**) v této podsíti.

Možná potřebujete statickou IP adresu pro webové servery, které vyžadují připojení SSL, ve kterých je certifikát SSL propojené k IP adrese. 

![POPISEK OBRÁZKU](./media/virtual-network-deploy-static-pip-scenario-include/figure1.png)

Provedením následujících kroků, abyste nasazení prostředí je znázorněno na obrázku výše.

