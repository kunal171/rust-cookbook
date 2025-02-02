## Parse command line arguments

[![clap-badge]][clap] [![cat-command-line-badge]][cat-command-line]

This application describes the structure of its command-line interface using
`clap`'s builder style. The [documentation] gives two other possible ways to
instantiate an application.

In the builder style, `with_name` is the unique identifier that `value_of` will
use to retrieve the value passed. The `short` and `long` options control the
flag the user will be expected to type; short flags look like `-f` and long
flags look like `--file`.

```rust,edition2018
use clap::{ arg, Command};

fn main() {
    let matches = Command::new("MyApp")
        .version("0.1.0")
        .author("Hackerman Jones <hckrmnjones@hack.gov>")
        .about("Teaches argument parsing")
        .arg(arg!(--file <VALUE>).short('f').required(true).value_name("A cool file"))
        .arg(arg!(--num <VALUE>).short('n').required(false).value_name("Five less than your favorite number"))
        .get_matches();

    let myfile = matches.get_one::<String>("file").expect("required");
    println!("The file passed is: {}", myfile);

    let num_str = matches.get_one::<String>("num");
    match num_str {
        None => println!("No idea what your favorite number is."),
        Some(s) => {
            match s.parse::<i32>() {
                Ok(n) => println!("Your favorite number must be {}.", n + 5),
                Err(_) => println!("That's not a number! {}", s),
            }
        }
    }
}
```

Usage information is generated by `clap`. The usage for the example application
looks like this.

```
My Test Program 0.1.0
Hackerman Jones <hckrmnjones@hack.gov>
Teaches argument parsing

USAGE:
    testing [OPTIONS]

FLAGS:
    -h, --help       Prints help information
    -V, --version    Prints version information

OPTIONS:
    -f, --file <file>     A cool file
    -n, --number <num>    Five less than your favorite number
```

We can test the application by running a command like the following.

```
$ cargo run -- -f myfile.txt -n 251
```

The output is:

```
The file passed is: myfile.txt
Your favorite number must be 256.
```

[documentation]: https://docs.rs/clap/
