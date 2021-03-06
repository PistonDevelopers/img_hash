img_hash [![Build Status](https://travis-ci.org/cybergeek94/img_hash.svg?branch=master)](https://travis-ci.org/cybergeek94/img_hash)
========

A library for getting perceptual hash values of images.

Thanks to Dr. Neal Krawetz for the outlines of the Average-Mean and DCT-Mean perceptual hash algorithms:  
http://www.hackerfactor.com/blog/?/archives/432-Looks-Like-It.html (Accessed August 2014)

Thanks to Emil Mikulic for the 2D Discrete Cosine Transform implementation in C, ported to Rust in `src/dct.rs`:  
http://unix4lyfe.org/dct/ (Implementation: http://unix4lyfe.org/dct/listing2.c) (Accessed August 2014)

Unfortunately, the AAN algorithm that provides `O(n log n)` performance didn't seem to be viable for arbitrary-length input vectors without massive code duplicaton. This shouldn't be a problem as hashing an image is still very fast on modern hardware. Bottlenecks are more likely in I/O or decoding the images from files.

Usage
=====
[Documentation on Rust-CI](http://rust-ci.org/cybergeek94/img_hash/doc/img_hash/index.html)


Add `img_hash` to your `Cargo.toml`:

    [dependencies.img_hash]
    git = "https://github.com/cybergeek94/img_hash"
    
Example program:

```rust
extern crate image;
extern crate img_hash;

use self::image;
use self::img_hash::ImageHash;

fn main() {
    let image1 = image::open(&Path::new("image1.png").unwrap()).unwrap();
    let image2 = image::open(&Path::new("image2.png").unwrap()).unwrap();
    
    // Second value is `hash_size`. The total bits in the hash will be = `hash_size` * `hash_size`.
    // Third value is `true` to use a fast hash, or `false` for a slower, more accurate DCT hash. DCT is recommended.
    let hash1 = ImageHash::hash(&image1, 8, false);
    let hash2 = ImageHash::hash(&image2, 8, false);
    
    println!("Image1 hash: {}", hash1.to_base64());
    println!("Image2 hash: {}", hash2.to_base64());
    
    println!("% Difference: {}", hash1.dist_ratio(&hash2));
}
```
    
