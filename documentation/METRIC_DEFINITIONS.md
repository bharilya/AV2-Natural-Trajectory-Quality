# Metric definitions

All quality annotations use original historical indices t = 0,…,49 at nominal Δt = 0.1 s. No future state, forecast error or model performance enters the quality measures. Original masks are preserved; trajectories are not interpolated or smoothed.

## Fields and units

The processed reference representation stores map-frame XY positions in meters, scalar speed vₜ in m/s, bounding-box heading θₜ in radians and a Boolean annotation-presence mask mₜ. Scalar speed is the norm of the original AV2 XY velocity, not a velocity vector or a position difference. An absent state may contain zero-filled tensor values but remains invalid. A measured zero speed or heading is not missing merely because it equals zero.

`history_length` is the number of valid historical observations, not elapsed span or the largest contiguous segment. Focal fields use the original category-3 prediction target. Original AV2 track categories are not noise labels.

## MSR: historical incompleteness

$$\mathrm{MSR}=1-\frac{1}{50}\sum_{t=0}^{49}m_t.$$

The dimensionless score includes leading and trailing absence. A 50-state history has MSR=0; one historical state has MSR=0.98. The score describes annotation availability, not its physical cause.

Primary agent annotations include only VEHICLE, PEDESTRIAN, MOTORCYCLIST, CYCLIST and BUS with at least one valid historical state. STATIC, BACKGROUND, CONSTRUCTION, RIDERLESS_BICYCLE and UNKNOWN are excluded. A stopped vehicle remains a permitted dynamic object type. Future-only agents do not enter the eligible historical population.


## VJ: Scalar-Speed Jerk Proxy

Let the set of valid consecutive historical triples be

$$
C = \{t \in \{0,\ldots,47\} :
m_t = m_{t+1} = m_{t+2} = 1\}.
$$

Then Velocity Jitter (VJ) is defined as

$$
\mathrm{VJ} =
\mathrm{median}_{t \in C}
\left(
\frac{|v_{t+2}-2v_{t+1}+v_t|}{\Delta t^2}
\right).
$$

Here, $\Delta t = 0.1$ seconds.

**Units:** m/s³.

VJ is a scalar-speed jerk proxy. It does not measure vector jerk,
position–velocity consistency, or confirmed measurement error.
Genuine driving manoeuvres can also increase VJ.


## HJ: changes in circular heading increments

Define

$$\operatorname{wrap}(\alpha)=((\alpha+\pi)\bmod 2\pi)-\pi,\qquad d_t=\operatorname{wrap}(\theta_{t+1}-\theta_t).$$

Then

$$\mathrm{HJ}=\operatorname{median}_{t\in C}\left(\frac{|\operatorname{wrap}(d_{t+1}-d_t)|}{\Delta t^2}\right).$$

Units are rad/s². HJ is an angular-acceleration proxy, not angular jerk. Box heading need not equal travel direction, particularly at low speeds. Circular differences handle angle representation crossings, while an actual near-π orientation flip can still produce a large value.

## Eligibility and numerical treatment

VJ and HJ require |C| ≥ 5. Adjacent triples may overlap; seven contiguous valid states yield five triples. Each triple must contain three genuinely adjacent valid states. A missing frame prevents a triple from spanning that frame. MSR remains available for otherwise eligible short histories, while unavailable VJ/HJ stay blank rather than becoming artificial zeros. All released focal histories contain 50 valid states and 48 triples.

The source calculation used float64 arithmetic on stored float32 samples. Their precision and any upstream annotation processing remain limitations. The original mask is never inferred from nonzero numeric values.

## Calibration and normalization

For each metric and object type, the existing training-agent distribution supplies P50 and P90 for selection. The released JSON also preserves additional candidate percentiles and reference groups from the original analysis; these do not alter the six fixed lists.

The recorded per-agent normalization is

$$z=\operatorname{clip}\!\left(\frac{x-Q^{\mathrm{train}}_{0.05}}{Q^{\mathrm{train}}_{0.95}-Q^{\mathrm{train}}_{0.05}},0,1\right).$$

A degenerate scale yields an unavailable normalized value. Global and object-type normalization candidates, and log1p candidates, remain in the calibration file. Selection uses raw score cutoffs, not a sum or average of normalized metrics. No combined quality score is defined.

`focal_VJ` and `focal_HJ` are focal-agent measures. `scene_MSR_score` is the contextual fraction defined in [SUBSET_SELECTION.md](SUBSET_SELECTION.md); it is distinct from focal-agent MSR, which is zero here. Generic manifest columns `selection_score`, `training_P50` and `training_P90` identify the score and boundaries used for that manifest's own selection metric.
