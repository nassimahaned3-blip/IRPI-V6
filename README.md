# IRPI-V6
\section{Normalisation de l'indice IRPI (version~6)}
\label{sec:irpi-normalisation}

\subsection{Formulation}

Pour une installation $i$, l'indice de risque explosion est le produit de
quatre termes bornés entre 0 et 1, majoré par un terme domino~:
\begin{equation}
\mathrm{IRPI}_i=\Pi_i\cdot\Phi_i\cdot\Omega_i\cdot\bigl(1+\Delta_i\bigr)
\end{equation}
avec, pour chaque terme~:
\begin{align}
\Pi_i &= \operatorname{clip}\!\left(\frac{\lambda_i-\lambda_{\mathrm{nom}}}
        {\lambda_{\max}-\lambda_{\mathrm{nom}}},\,0,\,1\right) \\
\Phi_i &= \operatorname{clip}\!\left(\frac{\log_{10}E_i-\log_{10}E_{\min}}
        {\log_{10}E_{\max}-\log_{10}E_{\min}},\,0,\,1\right),
        \qquad E_i=m_i\,\Delta H_i \\
\Omega_i &= \min\!\left(1,\;\frac{R_{\max,i}}{d_i}\right),
        \qquad R_{\max,i}=G_i\,e^{\varepsilon_i} \\
\Delta_i &= \frac{N_i}{M_i}\quad (\Delta_i=0\ \text{si}\ M_i=0)
\end{align}
où $\lambda_i$ est le taux de défaillance de l'équipement, $m_i$ la masse et
$\Delta H_i$ le pouvoir calorifique, $d_i$ la distance à la première cible,
et $G_i=Z(P_{\text{seuil}})\,W_{\mathrm{TNT},i}^{1/3}$ le rayon nominal donné
par la loi de Swisdak (section~1) avec
$W_{\mathrm{TNT},i}=m_i\,(\Delta H_i/4{,}68)\,\eta_i$. Le terme $M_i$ est le
nombre d'autres installations vulnérables (masse supérieure à un seuil
$m_{\mathrm{seuil}}$) et $N_i$ le nombre d'entre elles situées à une
distance euclidienne $d_{ij}$ inférieure au rayon domino
$R_{\mathrm{dom},i}=G_i(200~\mathrm{mbar})\,e^{\varepsilon_i}$.

\subsection{Pourquoi des bornes fixes}

Les versions~3 à~5 normalisaient par min-max sur l'ensemble des sites étudiés
($\Phi_i=(E_i-E_{\min}^{\text{jeu}})/(E_{\max}^{\text{jeu}}-E_{\min}^{\text{jeu}})$,
de même pour $\Omega_i$ et $\Delta_i$). Cette normalisation présente deux
défauts. D'une part, le score d'un site dépend des autres sites~: dans un
test, l'indice d'un même site passe de 0,3939 à 0,0161 lorsqu'on ajoute une
troisième installation, alors qu'avec des bornes fixes il reste égal à
0,3311. D'autre part, l'installation de plus faible énergie reçoit toujours
$\Phi=0$, donc un indice nul quelle que soit sa dangerosité réelle. Les bornes
fixes rendent le score d'une installation indépendant du jeu de données (hors
terme domino, qui dépend par nature du voisinage).

\subsection{Choix et conventions}

\begin{table}[H]
\centering
\caption{Paramètres de l'indice IRPI (version~6)}
\small
\begin{tabular}{@{}p{3.6cm}p{3.4cm}p{6.2cm}@{}}
\toprule
Paramètre & Valeur par défaut & Statut \\
\midrule
$\lambda_{\mathrm{nom}}$, $\lambda_{\max}$ & $10^{-4}$, $10^{-2}$ & convention (inchangée depuis la V3) \\
$E_{\min}$, $E_{\max}$ & $10^{3}$, $10^{7}$~MJ & convention de screening
  ($\approx$ 22~kg de propane, $\approx$ 220~t de combustible) \\
Seuil de surpression & 140~mbar & arrêté du 29 septembre 2005 \\
Seuil domino & 200~mbar & arrêté du 29 septembre 2005 \\
$m_{\mathrm{seuil}}$ & 1\,000~kg & convention \\
$\varepsilon_i$ & 1,0 & valeur retenue pour Feyzin, calée \emph{a posteriori}
  (enveloppe générale du rapport~: 0,337) \\
\bottomrule
\end{tabular}
\end{table}

\paragraph{Échelle logarithmique pour $\Phi$.} Le rayon d'effet varie en
$E^{1/3}$ et les énergies des installations couvrent plusieurs ordres de
grandeur~; une échelle logarithmique évite que les plus grandes installations
écrasent les autres.

\paragraph{Saturation de $\Omega$.} Dès que la cible se trouve dans le rayon
$R_{\max}$, l'exposition vaut 1~; en dehors, elle décroît proportionnellement
au rapport $R_{\max}/d$. Ce choix perd la distinction entre deux cibles toutes
deux situées à l'intérieur du rayon.

\paragraph{Terme domino.} Le seuil de 200~mbar est celui des effets domino de
l'arrêté du 29 septembre 2005, repris par l'approche INERIS de niveau~1
(Sandoval et al., 2019). $\Delta_i$ est une proportion et non un compte
normalisé par le maximum du jeu de sites.

\paragraph{Enveloppe $\varepsilon$.} Le rayon maximal $R_{\max}=G\,e^{\varepsilon}$
reprend la convention de la section~5. La valeur $\varepsilon=1$ est propre à
Feyzin et ne suffit pas à couvrir ses distances observées~; elle est un
paramètre ajustable de l'indice et non une constante physique. Elle n'est pas
appliquée au rayon thermique de la boule de feu, qui n'a pas été calibré avec
cette enveloppe.

\subsection{Vérifications réalisées}

Le code vérifie (i)~la loi de Swisdak sur le cas de Beyrouth (6,17~kPa à
1\,700~m pour 630~t, contre environ 6~kPa publié), (ii)~la décroissance des
$Z$ avec le seuil de surpression, (iii)~le rayon d'AZF à 140~mbar (321~m pour
28,8~t de TNT, contre 280 à 350~m selon l'INERIS), (iv)~l'égalité
$R_{\max}=G$ pour $\varepsilon=0$, (v)~l'indépendance du score vis-à-vis du jeu
de sites et (vi)~le calcul du terme domino sur un cas à trois installations.
Ces tests contrôlent la cohérence interne et quelques valeurs de référence~;
ils ne valident pas l'indice comme mesure du risque réel.

\subsection{Limites}

\begin{itemize}
    \item Les bornes $E_{\min}$ et $E_{\max}$ et la forme $\min(1,R/d)$ sont
    des conventions de screening, non calibrées sur des accidents.
    \item Pour les nuages de vapeur, l'équivalence TNT avec un rendement
    $\eta$ est une approximation~; la méthode préconisée par l'INERIS est la
    Multi-Énergie (indice de violence, volume encombré), non implémentée ici.
    La masse retenue est la masse relâchée, qui n'est pas celle du nuage
    inflammable.
    \item Le terme domino ne retient que la surpression et ne contient aucune
    probabilité d'escalade (niveau~1)~; le rayonnement thermique et les
    fragments ne sont pas pris en compte dans l'indice agrégé.
    \item L'agrégation des risques d'explosion et d'incendie en un indice
    unique n'est pas tranchée~: les deux sont présentés côte à côte.
\end{itemize}
