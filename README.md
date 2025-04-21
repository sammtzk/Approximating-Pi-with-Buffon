# Approximating-Pi-with-Buffon
A Monte Carlo method for calculating $\pi$ based on the needle problem posed by Georges-Louis Leclerc.

An overview of the work of this repository is provided below, but greater detail is provided in [needle.Rmd](needle.Rmd) and [needle.pdf](needle.pdf).

## Scenario

The Buffon's Needle problem poses the following question: If a needle is dropped
above a hardwood floor (supposing the wood strips are of equal width), what are
the chances that the needle lies across a line between two strips? This is
often reframed in terms of dropping sticks over an array of parallel and
equidistant lines.

The solution to this scenario, from a naive perspective, is unexpected. It
elucidates some key insights involving rotational symmetry and geometric
probability, and can even be taken advantage of to approximate pi.

Some references to this problem:

- https://en.wikipedia.org/wiki/Buffon%27s_needle_problem

- https://www.exploratorium.edu/exhibits/pi-toss

- https://youtu.be/sJVivjuMfWA?si=kNZymNkyLpTj2qE7

## Math

The probability can be calculated mathematically as follows:

$$
\begin{aligned}
\mathbb{P} &= \int_0^{\pi/2}\int_0^{l\sin(\theta)/2}\frac{4}{d\pi}\space{}dx\space{}d\theta \\
&= \int_0^{\pi/2}\left[\frac{4}{d\pi}x\right]_0^{l\sin(\theta)/2}\space{}d\theta \\
&= \int_0^{\pi/2}\frac{4l\sin(\theta)}{2d\pi}\space{}d\theta \\
&= \int_0^{\pi/2}\frac{2l\sin(\theta)}{d\pi}\space{}d\theta \\
&= \left[\frac{-2l\cos(\theta)}{d\pi}\right]_0^{\pi/2} \\
&= 0-\frac{-2l}{d\pi} \\
&= \frac{2l}{d\pi}.
\end{aligned}
$$

where

$$
\begin{aligned}
l &: \text{length of sticks} \\
s &: \text{number of sticks} \\
c &: \text{number of sticks crossing} \\
d &: \text{distance between lines}
\end{aligned}
$$

and

$$
\begin{aligned}
x &: \text{distance of stick center from nearest line} \\
\theta &: \text{rotation of stick along its center}
\end{aligned}
$$

## Simulations

This probability can be estimated using simulations as $\mathbb{P}\approx{}c/s$.

[needle.Rmd](needle.Rmd) contains code for this Monte Carlo method approach in **R**, implemented as follows:

```{r}
crossing <- function(length, angle, dist_center_from_line){
  if (angle %% pi > pi / 2){
    angle <- pi - (angle %% pi)
  }
  perpendicular_half_length <- abs(sin(angle)) * length / 2
  
  return(perpendicular_half_length >=  dist_center_from_line)
}
```

```{r}
buffon_crossings <- function(length, distance_between_lines, num_sims){
  angles <- runif(num_sims, 0, 2 * pi)
  dist_cent_to_line <- runif(num_sims, 0, distance_between_lines / 2)
  
  crossings <- 0
  for (i in 1: num_sims){
    crossings <- crossings + crossing(length, angles[i], dist_cent_to_line[i])
  }
  
  return(crossings)
}
```

## Pi

Using the same Monte Carlo simulation design, $\pi$ can be approximated like so:

$$
\mathbb{P}=\frac{2l}{d\pi},\mathbb{P}\approx\frac{c}{s}
\rightarrow\frac{c}{s}\approx\frac{2l}{d\pi} \\
\implies\pi\approx2\frac{ls}{cd},l\le{}d.
$$

```{r}
s <- 10000
l <- 1
d <- 1
c <- buffon_crossings(l, d, s)

print(paste("pi:", 2 * l * s / c / d))
```

## Extensions

This repository also explores an extension of the $\pi$ approximation problem in the sections titled **Targeting Pi Fractions** and **Targeting 22/7**.

These are included because Mario Lazzarini's experiment based on Buffon's Needle has gained a sort of infamy among mathematicians.
One such discussion can be read here: https://skepsis.nl/mainsite/inhoud/uploads/2022/05/Lazzarini.pdf.

In general, the **Targeting Pi Fractions** approach should be thought of as a sort of "neat trick", but should not be regarded as any sort of legitimate data science.
And of course analogous approaches in real world data applications are not to be endorsed.
Let this be a reminder to be wary of confirmation biases and *p*-hacking in our statistical analyses and any and all data science work!
