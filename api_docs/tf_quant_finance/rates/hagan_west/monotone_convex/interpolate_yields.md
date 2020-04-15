<div itemscope itemtype="http://developers.google.com/ReferenceObject">
<meta itemprop="name" content="tf_quant_finance.rates.hagan_west.monotone_convex.interpolate_yields" />
<meta itemprop="path" content="Stable" />
</div>

# tf_quant_finance.rates.hagan_west.monotone_convex.interpolate_yields

<!-- Insert buttons and diff -->

<table class="tfo-notebook-buttons tfo-api" align="left">
</table>

<a target="_blank" href="https://github.com/google/tf-quant-finance/blob/master/tf_quant_finance/rates/hagan_west/monotone_convex.py">View source</a>



Interpolates the yield curve to the supplied times.

```python
tf_quant_finance.rates.hagan_west.monotone_convex.interpolate_yields(
    interpolation_times, reference_times, yields=None, discrete_forwards=None,
    validate_args=False, dtype=None, name=None
)
```



<!-- Placeholder for "Used in" -->

  Applies the Hagan West procedure to interpolate either a zero coupon yield
  curve or a discrete forward curve to a given set of times.
  A zero coupon yield curve is specified by a set
  of times and the yields on zero coupon bonds expiring at those
  times. A discrete forward rate curve specifies the interest rate that
  applies between two times in the future as seen from the current time.
  The relation between the two sets of curve is as follows. Suppose the
  yields on zero coupon bonds expiring at times `[t_1, ..., t_n]` are
  `[r_1, ..., r_n]`, then the forward rate between time `[t_i, t_{i+1}]` is
  denoted `f(0; t_i, t_{i+1})` and given by

  ```None
    f(0; t_i, t_{i+1}) = (r_{i+1} t_{i+1} - r_i t_i) / (t_{i+1} - t_i)
  ```

  This function uses the Hagan West algorithm to perform the interpolation.
  The interpolation proceeds in two steps. Firstly the discrete forward
  curve is bootstrapped and an instantaneous forward curve is built. From the
  instantaneous forward curve, the interpolated yield values are inferred
  using the relation:

  ```None
    r(t) = (1/t) * Integrate[ f(s), 0 <= s <= t]
  ```

  The above equation connects the instantaneous forward curve `f(t)` to the
  yield curve `r(t)`. The Hagan West procedure uses the Monotone Convex
  interpolation to create a continuous forward curve. This is then integrated
  to compute the implied yield rate.

  For more details on the interpolation procedure, see Ref. [1].

#### Example

```python
  dtype = np.float64
  reference_times = np.array([1.0, 2.0, 3.0, 4.0], dtype=dtype)
  yields = np.array([5.0, 4.75, 4.53333333, 4.775], dtype=dtype)

  # Times for which the interpolated values are required.
  interpolation_times = np.array([0.25, 0.5, 1.0, 2.0], dtype=dtype)

  interpolated = interpolate_yields(
      interpolation_times, reference_times, yields=yields)
  # Produces [5.1171875, 5.09375, 5.0, 4.75]
```

#### References:

[1]: Patrick Hagan & Graeme West. Methods for Constructing a Yield Curve.
  Wilmott Magazine, pp. 70-81. May 2008.
  https://www.researchgate.net/profile/Patrick_Hagan3/publication/228463045_Methods_for_constructing_a_yield_curve/links/54db8cda0cf23fe133ad4d01.pdf

#### Args:


* <b>`interpolation_times`</b>: Non-negative rank 1 `Tensor` of any size. The times for
  which the interpolation has to be performed.
* <b>`reference_times`</b>: Strictly positive rank 1 `Tensor` of real dtype containing
  increasing values. The expiry times of the underlying zero coupon bonds.
* <b>`yields`</b>: Optional rank 1 `Tensor` of the same shape and dtype as
  `reference_times`, if supplied. The yield rate of zero coupon bonds
  expiring at the corresponding time in the `reference_times`. Either this
  argument or the `discrete_forwards` must be supplied (but not both).
  Default value: None.
* <b>`discrete_forwards`</b>: Optional rank 1 `Tensor` of the same shape and dtype as
  `reference_times`, if supplied. The `i`th component of the `Tensor` is the
  forward rate that applies between `reference_times[i-1]` and
  `reference_times[i]` for `i>0` and between time `0` and
  `reference_times[0]` for `i=0`. Either this argument or the `yields` must
  be specified (but not both).
  Default value: None.
* <b>`validate_args`</b>: Python bool. If true, adds control dependencies to check that
  the `times` are bounded by the `reference_times`.
  Default value: False
* <b>`dtype`</b>: `tf.Dtype` to use when converting arguments to `Tensor`s. If not
  supplied, the default Tensorflow conversion will take place. Note that
  this argument does not do any casting.
  Default value: None.
* <b>`name`</b>: Python `str` name prefixed to Ops created by this class.
  Default value: None which is mapped to the default name
    'interpolate_forward_rate'.


#### Returns:


* <b>`interpolated_forwards`</b>: Rank 1 `Tensor` of the same size and dtype as the
  `interpolation_times`. The interpolated instantaneous forwards at the
  `interpolation_times`.


#### Raises:

ValueError if neither `yields` nor `discrete_forwards` are specified or if
both are specified.