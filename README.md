# vcd2svg

vcd2svg is a public domain vcd to svg converter written in AWK.

## Usage

After compiling and running the simulation, call vcd2svg with the vcd file
as its input and redirecting its output to a svg file.  The variables that
should be displayed in the svg needs to be listed as arguments to vcd2svg.
For example, to show the variables `clk`, `data` and `out` on the waveform
from the file sim.vcd to the file sim.svg, run the following command.

	$ vcd2svg clk data out <sim.vcd >sim.svg

Each variable can be followed by a colon and a letter indicating the
notation for the labels.  This letter can be `h` for hexadecimal, `u`
for unsigned decimal, `d` for signed decimal, or `b` (the default) for
binary.

	$ vcd2svg clk:b data:u out:h <sim.vcd >sim.svg

When run without arguments, vcd2svg list all the variables in the input.

	$ vcd2svg <sim.vcd
	outer.clk
	outer.data
	outer.out
	outer.inner.signal
	outer.inner.acc
	outer.inner.out

Observe that the variables are listed prefixed with the period-delimited
path of modules that contains it.  For example, the variable `clk` is
used in the module `outer`; while the variable `a` is used in the module
`inner`, which is instantiated by the module outer.   The full path of a
variable can be used in the command-line arguments, for disambiguation,
in the case which a name is used for more than one variable.

	$ vcd2svg clk outer.inner.signal outer.inner.out <sim.vcd >sim.svg

Variables can be set with the -v option, as in -v VARIABLE=VALUE.
The following variables are supported by tbgen:

* `from`: Beginning time of the simulation.
* `to`:   Duration of the simulation.
* `zoom`: If between 0 and 1, zoom out; if larger than 1, zoom in.

For example, the following command generates a svg image showing the
waveform from time 10 to time 20, and sets the zoom factor to 1.4.
The `-v` option needs to be given each time a variable is set.

	$ vcd2svg -v from=10 -v to=20 -v zoom=1.4 clk data out 


## Example

A unknown value (`x`) is drawn in the resulting svg file as a series of
`x` characters.  High-impedance state (`z`) is drawn as a horizontal line.
The image below is a sample output from vcd2svg.

![example](https://user-images.githubusercontent.com/63266536/102781112-02b91380-438f-11eb-88ad-b2f66e98a841.png)

A svg image can be converted to png using inkscape:

	$ inkscape -z -b "#00000000" --export-filename sim.png sim.svg


## Bugs

* The width of the space for variable names is hardcoded to 100 pixels.
  This width can be insufficient for a variable with a long name.

* The label for the value of a variable may not fit in a wave pulse.
  For example, a hexadecimal label for a 64-bit value does not fit a
  pulse.   Setting the zoom to a value slightly larger than 1 (such
  as 1.5) solves this.
