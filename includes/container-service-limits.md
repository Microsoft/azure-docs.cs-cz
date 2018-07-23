| Prostředek | Výchozí omezení |
| --- | :--- |
| Maximální počet uzlů na cluster | 100 |
| Maximální počet podů na uzel ([základní síťové funkce s využitím Kubenet][basic-networking]) | 110 |
| Maximální počet podů na uzel ([pokročilé síťové funkce s využitím Azure CNI][advanced-networking]) | 30<sup>1</sup> |
| Maximální počet clusterů na předplatné | 100 |

<sup>1</sup> Tato hodnota se dá přizpůsobit nasazením šablony ARM. Příklady najdete [tady][arm-deployment-example].<br />

<!-- LINKS - Internal -->
[basic-networking]: ../articles/aks/networking-overview.md#basic-networking
[advanced-networking]: ../articles/aks/networking-overview.md#advanced-networking

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[arm-deployment-example]: https://github.com/Azure/AKS/blob/master/examples/vnet/02-aks-custom-vnet.json#L64-L69
