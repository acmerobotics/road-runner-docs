# Introduction

Part of the goal of Road Runner is to make advanced motion control techniques more accessible. However, for several reasons, the library is not immediately ready for use out of the box. For one, the Road Runner core intentionally has no dependencies on the [FTC SDK](https://github.com/ftctechnh/ftc_app) so it may be used in simulation and on other platforms. Consequently, there is some boilerplate required to integrate the library with the SDK hardware abstractions. Additionally, there are certain peculiarities to the FTC control system that affect the configuration.

The pages in this section contain more pragmatic material for applying the software in an FTC environment. Before moving on, it is essential that you have read and understand all of the material in the tour. Road Runner can easily become an impenetrable black box that is impossible to debug.

The quickstart can be found [here](https://github.com/acmerobotics/road-runner-quickstart).
